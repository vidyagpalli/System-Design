# URL Shortener System Design

## Problem Statement

Design a URL shortening service similar to Bitly.

### Functional Requirements

1. Given a long URL, generate and return a short URL.
2. When a user accesses the short URL, redirect them to the original long URL.
3. If the same long URL is submitted multiple times, generate a different unique short URL each time.
4. Support custom short URLs.
5. Support TTL (Time-To-Live) for URL mappings.
6. Provide analytics such as:

   * Number of clicks
   * Creation time
   * Expiration time
   * Geographic usage statistics

---

## Core Operations

### Encode

Converts a long URL into a short URL.

```text
Encode(Long URL) → Short URL
```

Example:

```text
https://www.example.com/products/item123

↓

https://bit.ly/1
```

### Decode

Converts a short URL back to the original long URL.

```text
Decode(Short URL) → Long URL
```

Example:

```text
https://bit.ly/1

↓

https://www.example.com/products/item123
```

---

## Data Structure

A key-value data structure can be used.

```text
Short URL Key  →  Long URL Value
```

Example:

```text
1  → https://www.example.com/page1
2  → https://www.example.com/page2
3  → https://www.example.com/page3
```

A Hash Table is suitable because it provides fast lookups.

```text
HashMap<ShortURL, LongURL>
```

---

## Basic Approach: Global Counter

Maintain a globally increasing counter.

```text
counter = 0
```

For every new URL:

```text
counter++
shortURL = counter
```

Examples:

```text
counter = 1 → bit.ly/1
counter = 2 → bit.ly/2
counter = 3 → bit.ly/3
```

---

## Pseudocode

### Encode

```java
global counter = 0

function encode(String longURL):

    counter++

    database[counter] = longURL

    return "https://bit.ly/" + String(counter)
```

### Decode

```java
function decode(String shortURL):

    id = extractId(shortURL)

    return database[id]
```

---

## Example

### Encoding

```text
Input:
https://www.example.com

counter = 1

Stored:
1 → https://www.example.com

Output:
https://bit.ly/1
```

### Decoding

```text
Input:
https://bit.ly/1

Lookup:
database[1]

Output:
https://www.example.com
```

---

## Database Schema

### URL Mapping Table

| Column      | Description         |
| ----------- | ------------------- |
| id          | Unique Counter      |
| short_url   | Generated Short URL |
| long_url    | Original URL        |
| created_at  | Creation Timestamp  |
| expiry_time | TTL Expiration Time |
| click_count | Number of Clicks    |

---

## High-Level Design

```text
Client
   |
   v
Load Balancer
   |
   v
Application Server
   |
   +---- Encode Service
   |
   +---- Decode Service
   |
   v
Database (Hash Table / Key-Value Store)
```

---

## Time Complexity

### Encode

```text
O(1)
```

### Decode

```text
O(1)
```

Average case using a hash table.

---

## Advantages

### Pros

1. Simple implementation.
2. No hash collisions.
3. Guaranteed unique short URLs.
4. Fast lookup using key-value storage.
5. Easy to scale initially.

---

## Limitations

### Cons

1. Short URLs become longer as the counter grows.

Example:

```text
1
10
100
1000
1000000000
```

2. URLs are predictable.

```text
bit.ly/100
bit.ly/101
bit.ly/102
```

An attacker can guess valid URLs.

3. Requires a centralized counter service.
4. Counter may become a bottleneck at very large scale.
5. Not suitable for globally distributed systems without additional design considerations.

---

## Possible Improvements

### Base62 Encoding

Instead of:

```text
1000000
```

Convert to Base62:

```text
4C92
```

Characters:

```text
0-9
A-Z
a-z
```

Benefits:

* Much shorter URLs
* Better user experience
* Supports billions of URLs

### Additional Features

* Custom aliases
* URL expiration (TTL)
* Analytics dashboard
* Rate limiting
* Caching using Redis
* Distributed ID generation
* QR Code generation

---

## Key Takeaway

The simplest URL shortener can be built using a globally increasing counter and a key-value store. Encoding inserts a mapping, while decoding performs a lookup. Although simple and collision-free, the approach suffers from predictable URLs and increasing URL length, which can be improved using Base62 encoding and distributed ID generation techniques.

