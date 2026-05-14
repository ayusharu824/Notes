
Prerequisite
1. caching
2. distributed system
It makes the system cheaper and faster

A content delivery network (CDN) is a system of distributed servers located across different geographic locations that deliver content to users from the nearest server instead of the main server

Without CDN

User (India) → Server (US) → Slow response ❌

With CDN

User (India) → Nearby CDN server (India) → Fast response ✅

👉 Result:
- Lower latency ⚡
- Faster load time
- Better user experience

How CDN works (step by step)

1. Step 1: User requests content
	Example : Load image /video /CSS file
2. Step 2: CDN checks nearest server
	1. if data exists -> return immediately.
	2. if not -> fetch from origin server.
3. Cache it
	1. Origin Server -> CDN -> User
	2. Next request:: CDN -> User(no origin hit)

What kind of content CDN delivers?

## ✅ Best for:

- Images 🖼️
- Videos 🎥
- CSS / JS files
- Static HTML

---
## ❌ Not ideal for:

- Dynamic API responses
- Real-time data

# Why CDN is NOT ideal for real-time API data

👉 Core reason:

> **CDN serves cached (stored) data, but real-time APIs need fresh (latest) data**

# 🚀 How often does CDN update data from origin server?

👉 **It depends on TTL (Time To Live)**

How to update UI from CDN after new deployment?
In microsoft azure you can do something like
Purge cache for /app.js