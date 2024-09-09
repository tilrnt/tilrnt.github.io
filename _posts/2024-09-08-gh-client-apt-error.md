---
layout: post
title: Expired GH client GPG keys
date: 2024-09-08 00:00:00
categories: ubuntu gh 
---

[GH client install issue]: https://github.com/cli/cli/issues/9569

When running `sudo apt update` recently, I received an error when trying to update the gh client:

{% highlight shell %}
W: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. GPG error: https://cli.github.com/packages stable InRelease: The following signatures were invalid: EXPKEYSIG 23F3D4EA75716059 GitHub CLI <opensource+cli@github.com>
W: Failed to fetch https://cli.github.com/packages/dists/stable/InRelease  The following signatures were invalid: EXPKEYSIG 23F3D4EA75716059 GitHub CLI <opensource+cli@github.com>
W: Some index files failed to download. They have been ignored, or old ones used instead.
{% endhighlight %}

This is due to the GPG key used to verify the .deb and .rpm repository expiring on the 6th September 2024. This is reported as a [GH client install issue].

To resolve it, one can run the provided script which downloads and reinstalls the new GPG key, fixing the error above:

{% highlight shell %}
# Check for wget, if not installed, install it
(type -p wget >/dev/null || (sudo apt update && sudo apt-get install wget -y)) \
    && sudo mkdir -p -m 755 /etc/apt/keyrings

# Set keyring path based on existence of /usr/share/keyrings/githubcli-archive-keyring.gpg
# If it is in the old location, use that, otherwise always use the new location.
if [ -f /usr/share/keyrings/githubcli-archive-keyring.gpg ]; then
    keyring_path="/usr/share/keyrings/githubcli-archive-keyring.gpg"
else
    keyring_path="/etc/apt/keyrings/githubcli-archive-keyring.gpg"
fi

echo "replacing keyring at ${keyring_path}"

# Download and set up the keyring
wget -qO- https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo tee "$keyring_path" > /dev/null \
    && sudo chmod go+r "$keyring_path"

# Idempotently add the GitHub CLI repository as an apt source
echo "deb [arch=$(dpkg --print-architecture) signed-by=$keyring_path] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null

# Update the package lists, which should now pass
sudo apt update
{% endhighlight %}

By running the above, I was able to run apt update and apt upgrade again.

Hope it helps!