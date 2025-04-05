---
layout: post
title: Compile and install python 3.13 on Ubuntu 20.04
date: 2025-03-11 00:00:00
categories: postgresql
---

In a recent project, I came across an issue of orphaned child entries in a database table after the parent entry is deleted. This is because the SQL schema doesn't have the `ON DELETE CASCADE` statement after the `REFERENCES` statement in the child table.

The new SQL statements for tables creation becomes:
```
CREATE TABLE IF NOT EXISTS chats (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255),
    token_count INT DEFAULT 0,
    created_at TIMESTAMPTZ,
    updated_at TIMESTAMPTZ
);

CREATE TABLE IF NOT EXISTS chathistory (
    id SERIAL PRIMARY KEY,
    role VARCHAR(255),
    message TEXT,
    model_name VARCHAR(255),
    files TEXT[],
    created_at TIMESTAMPTZ,
    chat_id INT,
    FOREIGN KEY(chat_id) REFERENCES chats(id) ON DELETE CASCADE
);
```

The above ensures that whenever a chat entry is deleted, the associated child entries in the chathistory table are also removed due to the delete cascade statement.