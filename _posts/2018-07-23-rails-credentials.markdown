---
layout: post
title:  "Using Rails ecnrypted credentials in Rails 5.2"
date:   2018-07-23 00:00:00 +0000
categories: ruby rails encryption
---

From Rails 5.2 onwards, there is no longer a `config/secrets.yml` file created
whenever a rails app is created. The default mechanism is to use `credentials`
to unify the management and storage of confidential information.

Within a new rails 5.2 app, you will see a `config/credentials.yml.enc` file
which is encrypted by default using the `config/master.key`. The `master.key` file
is the master key which is used to encrypt/decrypt data stored within the
`credentials.yml.enc` file and as such, it is added to `.gitignore` by default.

To view the contents of the encrypted file, you need to run the following:
```
EDITOR="vim" bin/rails credentials:show
```

This will display what was the contents of a file such as `config/secrets.yml`.

Mine contains the following out of the box:
```
# aws:
#   access_key_id: 123
#   secret_access_key: 345

# Used as the base secret for all MessageVerifiers in Rails, including the one protecting cookies.
secret_key_base: e86bd7e58727da9b818f0f5a8851e8e2c99679bb9ab0728e6d87fbf31febc26ff8b649dda74e8b5632d16521afb30066254a2e4d6869e2fb57cb93f072b3e0ef
```

To edit/add new entries to the file:
```
EDITOR="vim" bin/rails credentials:edit
```

This will allow you to edit/update the entries within `config/credentials.yml.enc`

You can still use the old YAML syntax to declare variables. For example:
```
EDITOR="vim" bin/rails credentials:edit

# Add the following snippet below
foo:
  bar: baz
```

To access any of the data during runtime, we can use `Rails.application.credentials`, which returns a `ActiveSupport::EncryptedConfiguration` object

For example, to access the default secret_key_base:
```
Rails.application.credentials.secret_key_base
```

To access nested values, we can use:
```
Rails.application.credentials.foo[:bar] # => baz
```
