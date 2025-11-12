
# Cursor-Based Pagination - Spring Boot Guide

## What is Cursor Pagination?

A cursor is a **bookmark** pointing to a specific item. Instead of "page 3", you say "give me items after this bookmark".

**Benefits:**
- ✅ Consistent results when data changes
- ✅ Fast performance (uses indexes efficiently)
- ✅ Perfect for real-time feeds

---

## Spring Boot Implementation

### 1. DTOs

**NumberResponse.java**
```java

package com.example.demo.dto;

import java.util.List;

public class NumberResponse {
    private List<Integer> data;
    private PaginationInfo pagination;

    public NumberResponse(List<Integer> data, PaginationInfo pagination) {
        this.data = data;
        this.pagination = pagination;
    }

    public List<Integer> getData() { return data; }
    public void setData(List<Integer> data) { this.data = data; }
    public PaginationInfo getPagination() { return pagination; }
    public void setPagination(PaginationInfo pagination) { this.pagination = pagination; }
}

```

**PaginationInfo.java**
```java
package com.example.demo.dto;

public class PaginationInfo {
    private String nextCursor;
    private String prevCursor;
    private boolean hasNext;
    private boolean hasPrev;

    public PaginationInfo(String nextCursor, String prevCursor, boolean hasNext, boolean hasPrev) {
        this.nextCursor = nextCursor;
        this.prevCursor = prevCursor;
        this.hasNext = hasNext;
        this.hasPrev = hasPrev;
    }

    public String getNextCursor() { return nextCursor; }
    public void setNextCursor(String nextCursor) { this.nextCursor = nextCursor; }
    public String getPrevCursor() { return prevCursor; }
    public void setPrevCursor(String prevCursor) { this.prevCursor = prevCursor; }
    public boolean isHasNext() { return hasNext; }
    public void setHasNext(boolean hasNext) { this.hasNext = hasNext; }
    public boolean isHasPrev() { return hasPrev; }
    public void setHasPrev(boolean hasPrev) { this.hasPrev = hasPrev; }
}
```

---

### 2. Controller

**NumberController.java**
```java
package com.example.demo.controller;

import com.example.demo.dto.NumberResponse;
import com.example.demo.dto.PaginationInfo;
import org.springframework.web.bind.annotation.*;
import java.util.ArrayList;
import java.util.Base64;
import java.util.List;

@RestController
@RequestMapping("/api")
public class NumberController {

    private static final int MAX_NUMBER = 100;

    @GetMapping("/numbers")
    public NumberResponse getNumbers(
            @RequestParam(defaultValue = "10") int limit,
            @RequestParam(required = false) String after,
            @RequestParam(required = false) String before
    ) {
        Integer afterNum = after != null ? decodeCursor(after) : null;
        Integer beforeNum = before != null ? decodeCursor(before) : null;

        List<Integer> data = new ArrayList<>();
        int startNum;

        if (afterNum != null) {
            // Get numbers AFTER cursor (next page)
            startNum = afterNum + 1;
            for (int i = startNum; i <= MAX_NUMBER && data.size() < limit; i++) {
                data.add(i);
            }
        } 
        else if (beforeNum != null) {
            // Get numbers BEFORE cursor (previous page)
            startNum = beforeNum - 1;
            for (int i = startNum; i >= 1 && data.size() < limit; i--) {
                data.add(0, i);
            }
        } 
        else {
            // First page
            startNum = 1;
            for (int i = startNum; i <= MAX_NUMBER && data.size() < limit; i++) {
                data.add(i);
            }
        }

        boolean hasNext = !data.isEmpty() && data.get(data.size() - 1) < MAX_NUMBER;
        boolean hasPrev = afterNum != null || beforeNum != null;

        String nextCursor = hasNext ? encodeCursor(data.get(data.size() - 1)) : null;
        String prevCursor = hasPrev && !data.isEmpty() ? encodeCursor(data.get(0)) : null;

        PaginationInfo pagination = new PaginationInfo(nextCursor, prevCursor, hasNext, hasPrev);
        return new NumberResponse(data, pagination);
    }

    private String encodeCursor(int number) {
        return Base64.getEncoder().encodeToString(String.valueOf(number).getBytes());
    }

    private Integer decodeCursor(String cursor) {
        try {
            byte[] decoded = Base64.getDecoder().decode(cursor);
            return Integer.parseInt(new String(decoded));
        } catch (Exception e) {
            return null;
        }
    }
}
```

---

## API Examples

### Request 1: First Page
```http
GET /api/numbers?limit=5
```
**Response:**
```json
{
  "data": [1, 2, 3, 4, 5],
  "pagination": {
    "nextCursor": "NQ==",
    "prevCursor": null,
    "hasNext": true,
    "hasPrev": false
  }
}
```

### Request 2: Next Page
```http
GET /api/numbers?limit=5&after=NQ==
```
**Response:**
```json
{
  "data": [6, 7, 8, 9, 10],
  "pagination": {
    "nextCursor": "MTA=",
    "prevCursor": "Ng==",
    "hasNext": true,
    "hasPrev": true
  }
}
```

### Request 3: Previous Page
```http
GET /api/numbers?limit=5&before=MTE=
```
**Response:**
```json
{
  "data": [6, 7, 8, 9, 10],
  "pagination": {
    "nextCursor": "MTA=",
    "prevCursor": "Ng==",
    "hasNext": true,
    "hasPrev": true
  }
}
```

### Request 4: Last Page
```http
GET /api/numbers?limit=5&after=OTU=
```
**Response:**
```json
{
  "data": [96, 97, 98, 99, 100],
  "pagination": {
    "nextCursor": null,
    "prevCursor": "OTY=",
    "hasNext": false,
    "hasPrev": true
  }
}
```

---

## How It Works

```
Numbers: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, ..., 100]

Step 1: ?limit=5
        [1, 2, 3, 4, 5] → cursor points to 5

Step 2: ?limit=5&after=<cursor_5>
                      [6, 7, 8, 9, 10] → cursor points to 10

Step 3: ?limit=5&after=<cursor_10>
                                    [11, 12, 13, 14, 15]

Step 4: ?limit=5&before=<cursor_11>
                      [6, 7, 8, 9, 10] (back to previous)
```

---

## Key Concepts

| Parameter | Purpose | Example |
|-----------|---------|---------|
| `limit` | Number of items to return | `?limit=10` |
| `after` | Get items after cursor (next page) | `?after=NQ==` |
| `before` | Get items before cursor (prev page) | `?before=MTE=` |

**Cursor Encoding:**
- `5` → Base64 encode → `NQ==`
- `NQ==` → Base64 decode → `5`

**Logic:**
1. **No cursor** → Start from beginning
2. **`after` cursor** → Get items > cursor value
3. **`before` cursor** → Get items < cursor value
4. **`hasNext`** → Check if more items exist
5. **`hasPrev`** → True if any cursor was used

---

## Testing

```bash
# First page
curl "http://localhost:8080/api/numbers?limit=5"

# Next page
curl "http://localhost:8080/api/numbers?limit=5&after=NQ=="

# Previous page
curl "http://localhost:8080/api/numbers?limit=5&before=MTE="
```

---

## Database Implementation (Bonus)

For real database queries:

```java
// Next page
@Query("SELECT p FROM Post p WHERE p.id < :afterId ORDER BY p.id DESC")
List<Post> findAfter(@Param("afterId") Long afterId, Pageable pageable);

// Previous page
@Query("SELECT p FROM Post p WHERE p.id > :beforeId ORDER BY p.id ASC")
List<Post> findBefore(@Param("beforeId") Long beforeId, Pageable pageable);
```

```sql
-- Next page SQL
SELECT * FROM posts WHERE id < ? ORDER BY id DESC LIMIT 10;

-- Previous page SQL
SELECT * FROM posts WHERE id > ? ORDER BY id ASC LIMIT 10;
```

---

## When to Use

| Use Case | Recommended |
|----------|-------------|
| Social media feeds | ✅ Cursor |
| Infinite scroll | ✅ Cursor |
| Real-time data | ✅ Cursor |
| Admin dashboards | ❌ Use offset |
| Need page numbers | ❌ Use offset |
```
