# **API Design Standards**

เพื่อให้ทีมพัฒนามีมาตรฐานเดียวกันและลดความสับสนในการพัฒนา API ต่อไปนี้คือ **API Design Standards**:

---

## **1. General Guidelines**

### **1.1 RESTful API**
- ใช้รูปแบบ **RESTful API**:
  - **Stateless:** ทุกคำขอ (Request) ควรมีข้อมูลที่ครบถ้วน ไม่พึ่งพา Session
  - **Resource-Oriented:** อิงกับทรัพยากร (Resource) เช่น `products`, `users`, `orders`

### **1.2 Naming Conventions**
- ใช้ **snake_case** สำหรับ field names (e.g., `user_id`, `created_at`)
- ใช้ **kebab-case** สำหรับ URL paths (e.g., `/api/v1/products`)
- ใช้ **lowercase** สำหรับชื่อทั้งหมด

### **1.3 HTTP Methods**
- กำหนดการใช้งาน HTTP Methods ชัดเจน:
  - **GET:** ดึงข้อมูล (Retrieve)
  - **POST:** สร้างข้อมูลใหม่ (Create)
  - **PUT:** อัปเดตข้อมูลทั้งหมด (Replace)
  - **PATCH:** อัปเดตข้อมูลบางส่วน (Partial Update)
  - **DELETE:** ลบข้อมูล (Delete)

---

## **2. URL Design**

### **2.1 Base URL**
- ระบุ Base URL ที่ชัดเจน:
  ```plaintext
  https://api.example.com/v1/
  ```

### **2.2 Resource Structure**
- ใช้ **nouns** สำหรับชื่อทรัพยากร (ไม่ใช้ verbs):
  ```plaintext
  /products (✅)
  /getProducts (❌)
  ```
- ใช้ **plural nouns** สำหรับทรัพยากร:
  ```plaintext
  /users (✅)
  /user (❌)
  ```

### **2.3 Path Parameters**
- ใช้ Path Parameters สำหรับระบุทรัพยากรเฉพาะ:
  ```plaintext
  /products/{id} (✅)
  /products?id=123 (❌)
  ```

### **2.4 Query Parameters**
- ใช้ Query Parameters สำหรับ **Filters**, **Sorting**, หรือ **Pagination**:
  ```plaintext
  /products?page=1&pageSize=10&sort=name&filter=available
  ```

### **2.5 Nested Resources**
- ใช้ Path สำหรับความสัมพันธ์:
  ```plaintext
  /users/{user_id}/orders
  ```

---

## **3. Request and Response**

### **3.1 Request Body**
- ใช้ **JSON** สำหรับ Request Body
- ใช้ **snake_case** สำหรับชื่อ field

ตัวอย่าง:
```json
{
  "name": "Laptop",
  "price": 1200.99,
  "description": "A high-performance laptop"
}
```

### **3.2 Response Format**
- Response ต้องมีโครงสร้างแบบมาตรฐาน:
```json
{
  "success": true,
  "data": {...},
  "error": {
    "code": 0,
    "message": "",
    "details": ""
  },
  "meta": {
    "timestamp": "2025-01-16T12:00:00Z",
    "requestId": "abc123"
  }
}
```

### **3.3 Pagination**
- ระบุ Pagination Metadata ใน Response:
```json
{
  "success": true,
  "data": [...],
  "meta": {
    "currentPage": 1,
    "pageSize": 10,
    "totalPages": 5,
    "totalItems": 50
  }
}
```

---

## **4. HTTP Status Codes**

### **4.1 Success**
| **Code** | **Description**          |
|----------|--------------------------|
| 200      | OK                       |
| 201      | Created                  |
| 204      | No Content               |

### **4.2 Client Errors**
| **Code** | **Description**                   |
|----------|-----------------------------------|
| 400      | Bad Request                       |
| 401      | Unauthorized                      |
| 403      | Forbidden                         |
| 404      | Not Found                         |
| 409      | Conflict                          |

### **4.3 Server Errors**
| **Code** | **Description**          |
|----------|--------------------------|
| 500      | Internal Server Error    |
| 503      | Service Unavailable      |

---

## **5. Error Handling**

### **5.1 Response Format**
- ใช้โครงสร้างที่เข้าใจง่าย:
```json
{
  "success": false,
  "error": {
    "code": 1001,
    "message": "Validation Error",
    "details": "The 'price' field is required."
  }
}
```

### **5.2 Internal Error Codes (หรือ Business Codes)**
| **Error Code** | **Category**        | **Description**                           |
|----------------|---------------------|-------------------------------------------|
| `1000`         | Validation          | Missing required field.                   |
| `2000`         | Authentication      | Invalid token.                            |
| `3000`         | Resource            | Resource not found.                       |
| `5000`         | Server              | Internal server error.                    |

-----------------------------------------------------------------------------
**Internal Error Codes** คือรหัสที่ใช้ระบุข้อผิดพลาดหรือสถานะที่เกิดขึ้นภายในระบบ โดยเฉพาะสำหรับการสื่อสารกับผู้พัฒนาหรือลูกค้า เพื่อช่วยให้เข้าใจสาเหตุและการแก้ไขข้อผิดพลาดได้ง่ายขึ้น

---

### **แนวทางการออกแบบ Internal Error Codes**

1. **โครงสร้างที่ชัดเจน:**
   - แบ่งกลุ่มรหัสตามประเภทของข้อผิดพลาด เช่น Validation, Authentication, Authorization, Resource, Business Logic และ Server Error
   - ใช้เลขที่อ่านง่าย เช่น `1001`, `2001`, `3001`

2. **หมวดหมู่ที่เป็นมาตรฐาน:**
   - **1xx:** Validation Errors (ข้อผิดพลาดที่เกี่ยวกับข้อมูล)
   - **2xx:** Authentication & Authorization Errors (ข้อผิดพลาดเกี่ยวกับสิทธิ์)
   - **3xx:** Resource Errors (ข้อผิดพลาดเกี่ยวกับทรัพยากร)
   - **4xx:** Business Logic Errors (ข้อผิดพลาดเชิงธุรกิจ)
   - **5xx:** Server Errors (ข้อผิดพลาดภายในเซิร์ฟเวอร์)

3. **คำอธิบายที่สื่อความหมาย:**
   - ใช้คำอธิบายที่ระบุปัญหาอย่างชัดเจน เช่น `Resource not found` หรือ `Invalid input format`

---

### **ตัวอย่าง Internal Error Codes**

#### **Validation Errors (1xx)**
| **Code** | **Category**  | **Description**                                      |
|----------|---------------|------------------------------------------------------|
| 1000     | Validation    | Missing required field.                              |
| 1001     | Validation    | Invalid input format.                                |
| 1002     | Validation    | Field value exceeds allowed limit.                  |

#### **Authentication & Authorization Errors (2xx)**
| **Code** | **Category**  | **Description**                                      |
|----------|---------------|------------------------------------------------------|
| 2000     | Authentication| Invalid token.                                       |
| 2001     | Authentication| Token expired.                                       |
| 2002     | Authorization | Access denied.                                       |

#### **Resource Errors (3xx)**
| **Code** | **Category**  | **Description**                                      |
|----------|---------------|------------------------------------------------------|
| 3000     | Resource      | Resource not found.                                  |
| 3001     | Resource      | Duplicate resource detected.                         |

#### **Business Logic Errors (4xx)**
| **Code** | **Category**  | **Description**                                      |
|----------|---------------|------------------------------------------------------|
| 4000     | Business      | Operation not allowed under current business rules.  |
| 4001     | Business      | Insufficient balance for transaction.               |

#### **Server Errors (5xx)**
| **Code** | **Category**  | **Description**                                      |
|----------|---------------|------------------------------------------------------|
| 5000     | Server        | Internal server error.                               |
| 5001     | Server        | Database connection failure.                         |

---

### **ตัวอย่าง Response เมื่อเกิดข้อผิดพลาด**

#### **Response Format**
```json
{
  "success": false,
  "error": {
    "code": 1001,
    "message": "Validation Error",
    "details": "The 'email' field must be a valid email address."
  },
  "meta": {
    "timestamp": "2025-01-16T12:00:00Z",
    "requestId": "abc123"
  }
}
```

#### **Business Logic Error Example**
```json
{
  "success": false,
  "error": {
    "code": 4001,
    "message": "Insufficient balance",
    "details": "Your account balance is too low to complete the transaction."
  },
  "meta": {
    "timestamp": "2025-01-16T12:05:00Z",
    "requestId": "xyz456"
  }
}
```

---

### **Best Practices สำหรับ Internal Error Codes**
1. **Consistency:** ใช้รูปแบบรหัสข้อผิดพลาดที่เป็นมาตรฐานและสม่ำเสมอในทุกระบบ
2. **Documentation:** จัดทำเอกสารอธิบาย Error Codes ทั้งหมด พร้อมคำแนะนำในการแก้ไข
3. **Localization:** คำอธิบายข้อผิดพลาดควรรองรับหลายภาษา ถ้าระบบใช้งานในหลายประเทศ
4. **Mapping to HTTP Status Codes:**
   - Error Code ควรสอดคล้องกับ HTTP Status Codes เช่น:
     - `404 Not Found` → `3000 Resource not found`
     - `400 Bad Request` → `1001 Validation Error`

---

### **ตัวอย่างเอกสารสำหรับทีมพัฒนา**

| **Error Code** | **HTTP Status** | **Description**                        | **Solution**                      |
|----------------|-----------------|----------------------------------------|-----------------------------------|
| 1001           | 400 Bad Request| Invalid input format.                 | Check and correct the input data.|
| 2001           | 401 Unauthorized| Token expired.                        | Re-authenticate to get a new token.|
| 3000           | 404 Not Found  | Resource not found.                   | Ensure the resource ID is correct.|
| 5001           | 500 Internal Server Error| Database connection failure.    | Check database connectivity.     |

---

การใช้ **Internal Error Codes** อย่างถูกต้องช่วยลดความซับซ้อนในการ Debug และทำให้ระบบมีมาตรฐานที่ชัดเจน ซึ่งสำคัญมากสำหรับทีมพัฒนา หากต้องการตัวอย่างเพิ่มเติม แจ้งมาได้เลยครับ!
--------------------------------------------------------------------------------

## **6. API Versioning**

### **6.1 URL Versioning**
- ใช้เวอร์ชันใน URL:
  ```plaintext
  /api/v1/products
  ```

### **6.2 Deprecation**
- แจ้งเตือนเมื่อ API เวอร์ชันเก่าใกล้ถูกยกเลิก:
```plaintext
X-API-Deprecated: true
X-API-Deprecation-Date: 2025-12-31
```

---

## **7. Documentation Standards**

### **7.1 Tools**
- ใช้ **Swagger** หรือ **OpenAPI** เพื่อสร้างเอกสาร
- ใช้ **Postman** สำหรับการทดสอบ

### **7.2 Content**
- ระบุ Endpoint, Method, Query Parameters, Request Body, Response Body
- ระบุ Status Codes ที่เกี่ยวข้อง
- ตัวอย่าง Request และ Response พร้อมตัวอย่างข้อผิดพลาด

---

## **8. Security**

### **8.1 Authentication**
- ใช้ **JWT** (JSON Web Token) สำหรับ Authentication:
  ```plaintext
  Authorization: Bearer <token>
  ```

### **8.2 HTTPS**
- บังคับใช้ HTTPS ในทุก API

### **8.3 Rate Limiting**
- กำหนด Rate Limiting เพื่อลดความเสี่ยงจากการโจมตี:
  ```plaintext
  X-RateLimit-Limit: 1000
  X-RateLimit-Remaining: 500
  X-RateLimit-Reset: 2025-01-16T13:00:00Z
  ```

---

## **ตัวอย่างมาตรฐานการออกแบบ**

### **Endpoint: Create Product**
#### **Description:**
สร้างสินค้าใหม่

#### **Method:**
`POST`

#### **URL:**
`/api/v1/products`

#### **Request Body:**
```json
{
  "name": "Laptop",
  "price": 1200.99,
  "description": "A high-performance laptop"
}
```

#### **Response:**
- **Success (201 Created):**
```json
{
  "success": true,
  "data": {
    "id": 1,
    "name": "Laptop",
    "price": 1200.99,
    "description": "A high-performance laptop",
    "createdAt": "2025-01-16T12:00:00Z"
  }
}
```
- **Error (400 Bad Request):**
```json
{
  "success": false,
  "error": {
    "code": 1001,
    "message": "Validation Error",
    "details": "The 'price' field must be greater than 0."
  }
}
```

---

หากคุณต้องการปรับแต่งเพิ่มเติมหรือเพิ่มตัวอย่างในส่วนใด แจ้งมาได้เลยครับ!
