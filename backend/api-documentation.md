# Backend API Documentation

## 📝 Overview
The Lead Processor backend provides a RESTful API for lead management, opportunity tracking, user management, and CRM integration. Built with Express.js and TypeScript, the API follows RESTful conventions with comprehensive error handling and validation.

> **📋 Complete API Collection**: The following documentation covers some of the key API endpoints. For a comprehensive list of all available APIs, please refer to our [Postman Collection](https://wanderonlead-flow-team.postman.co/workspace/wanderon.lead-flow-team-Workspa~694c924e-c65a-49bd-8910-c6bc1fb78a7f/collection/40486482-9c0f4f7c-ac95-417d-8e8f-2e22d23467c2?action=share&source=copy-link&creator=40486482) which contains detailed examples and additional endpoints.

## 🔗 Base Configuration
- **Base URL**: `http://localhost:3000` (development)
- **Content-Type**: `application/json`
- **Authentication**: HTTP-only cookies (Bearer token support to be implemented if needed)
- **Rate Limiting**: Redis-based throttling

## 🔐 Authentication

### Login
```http
POST /auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "status_code": 200,
  "message": "Login successful",
  "data": {
    "user": {
      "_id": "user_id",
      "email": "user@example.com",
      "firstName": "John",
      "lastName": "Doe",
      "department": "SALES"
    },
    "token": "jwt_token_here"
  }
}
```

### Logout
```http
POST /auth/logout
Authorization: Bearer <token>
```

### Protected Route Pattern
All protected routes require authentication via HTTP-only cookies:
```http
Cookie: authToken=<jwt_token>
```

> **Note**: Bearer token authentication (`Authorization: Bearer <token>`) is not currently implemented but can be added if needed in the future.

## 👥 User Management

### Get Users
```http
GET /user?departments[]=SALES&search=john
Cookie: authToken=<jwt_token>
```

**Query Parameters:**
- `departments[]`: Filter by department (SALES, PSV, WEST, etc.)
- `search`: Search by name or email

**Response:**
```json
{
  "status_code": 200,
  "data": {
    "users": [
      {
        "_id": "user_id",
        "firstName": "John",
        "lastName": "Doe",
        "email": "john@example.com",
        "department": "SALES",
        "phone": {
          "countryCode": "+91",
          "number": "9876543210"
        }
      }
    ]
  }
}
```

### Get User Profile
```http
GET /user/profile
Cookie: authToken=<jwt_token>
```

## 🎯 Availability Management

### Get Availability Processes
```http
GET /availability
Cookie: authToken=<jwt_token>
```

**Response:**
```json
{
  "status_code": 200,
  "data": {
    "availabilityProcesses": [
      {
        "id": "SALES",
        "name": "Sales Availability"
      },
      {
        "id": "PSV",
        "name": "PSV Availability"
      }
    ]
  }
}
```

### Get Availability by Process
```http
GET /availability/SALES?pageNo=1&pageSize=50
Cookie: authToken=<jwt_token>
```

**Response:**
```json
{
  "status_code": 200,
  "data": {
    "availabilities": [
      {
        "_id": "availability_id",
        "user": {
          "_id": "user_id",
          "name": "John Doe",
          "email": "john@example.com",
          "phone": {
            "countryCode": "+91",
            "number": "9876543210"
          }
        },
        "isAvailable": true,
        "leadCount": 5,
        "destinations": [
          {
            "_id": "dest_id",
            "name": "Kashmir"
          }
        ],
        "source": ["WEBSITE", "PAID_ADS"],
        "process": "SALES"
      }
    ],
    "totalCount": 25,
    "perPage": 50
  }
}
```

### Update Availability
```http
PATCH /availability/SALES/availability_id
Cookie: authToken=<jwt_token>
Content-Type: application/json

{
  "isAvailable": false,
  "destinations": [
    {
      "_id": "dest_id",
      "name": "Kashmir"
    }
  ],
  "source": ["WEBSITE"]
}
```

**Enhanced Features (v1.0.0+):**
- `source` array supports platform-specific assignment (WEBSITE, INSTAGRAM, DC, PAID_ADS, WHATSAPP_BOT, GOOGLE_ADS_WEST, JUNK_AUTOMATION)
- System prioritizes POCs configured for specific source platforms
- Automatic fallback to destination-only POCs when no source-specific POCs available
- Load balancing based on lead count across available POCs

### Create New Availability
```http
POST /availability/SALES
Cookie: authToken=<jwt_token>
Content-Type: application/json

{
  "firstName": "Jane",
  "lastName": "Smith",
  "email": "jane@example.com",
  "number": "9876543210"
}
```

## 🗺️ Destinations

### Get All Destinations
```http
GET /destinations
```

**Response:**
```json
{
  "status_code": 200,
  "data": {
    "destinations": [
      {
        "_id": "dest_id",
        "name": "Kashmir",
        "slugs": ["kashmir", "jammu-kashmir"],
        "deleted": false
      }
    ]
  }
}
```

### Add Destination
```http
POST /destinations
Cookie: authToken=<jwt_token>
Content-Type: application/json

{
  "name": "Ladakh",
  "slugs": ["ladakh", "leh-ladakh"]
}
```

## 🔗 Web URLs & Ranking

### Get Web URLs
```http
GET /weburls?destinationId=dest_id
Cookie: authToken=<jwt_token>
```

### Update URL Ranking
```http
PATCH /weburls/ranking
Cookie: authToken=<jwt_token>
Content-Type: application/json

{
  "destinationId": "dest_id",
  "rankings": [
    {
      "url": "https://example.com/kashmir",
      "rank": 1
    }
  ]
}
```

## 🎯 Opportunity Management

### Get Opportunities (CRM)
```http
GET /crm/opportunity?stage=NEW&limit=20&offset=0&startDate=2024-01-01&endDate=2024-12-31
Cookie: authToken=<jwt_token>
```

**Query Parameters:**
- `stage`: Filter by opportunity stage
- `salesPersons[]`: Filter by salesperson IDs
- `destinations[]`: Filter by destination IDs
- `startDate`, `endDate`: Date range filter
- `limit`, `offset`: Pagination
- `sortBy`, `sortOrder`: Sorting options

**Response:**
```json
{
  "status_code": 200,
  "data": {
    "opportunities": [
      {
        "_id": "opp_id",
        "leadId": "lead_id",
        "customerName": "John Customer",
        "customerPhone": "+919876543210",
        "customerEmail": "customer@example.com",
        "destinations": ["Kashmir", "Ladakh"],
        "stage": "NEW",
        "salesPerson": {
          "_id": "sales_id",
          "name": "Sales Person"
        },
        "createdAt": "2024-01-15T10:30:00Z",
        "followUpDate": "2024-01-16T10:00:00Z"
      }
    ],
    "totalCount": 150,
    "limit": 20,
    "offset": 0
  }
}
```

### Update Opportunity Status
```http
PATCH /crm/opportunity/opp_id/status
Cookie: authToken=<jwt_token>
Content-Type: application/json

{
  "stage": "FOLLOWUP",
  "subStage": "CALLBACK_REQUESTED",
  "notes": "Customer requested callback tomorrow",
  "followUpDate": "2024-01-16T10:00:00Z"
}
```

### Get Opportunity Details
```http
GET /crm/opportunity/opp_id
Cookie: authToken=<jwt_token>
```

### Add Opportunity Note
```http
POST /crm/opportunity/opp_id/notes
Cookie: authToken=<jwt_token>
Content-Type: application/json

{
  "note": "Customer showed interest in Kashmir package",
  "isImportant": true
}
```

## 📊 Analytics & Reporting

### Get Daily Performance Breakdown
```http
GET /crm/opportunity/dailyPerformanceBreakdown?startDate=2024-01-01T00:00:00Z&endDate=2024-01-31T23:59:59Z&destinations[]=dest_id&salesPersons[]=sales_id
Cookie: authToken=<jwt_token>
```

### Get Team Performance
```http
GET /crm/opportunity/teamPerformance?startDate=2024-01-01&endDate=2024-01-31
Cookie: authToken=<jwt_token>
```

## 🔔 Booking Management

### Get Booking Details
```http
GET /booking/WON123456
Cookie: authToken=<jwt_token>
```

**Response:**
```json
{
  "status_code": 200,
  "data": {
    "bookingId": "WON123456",
    "customerName": "John Doe",
    "destination": "Kashmir",
    "bookingDate": "2024-01-15",
    "amount": 50000,
    "status": "CONFIRMED"
  }
}
```

## 🎛️ Lead Source Management

### Get Lead Source Platforms
```http
GET /leadSource/platforms
```

**Response:**
```json
{
  "status_code": 200,
  "data": {
    "platforms": [
      {
        "id": "WEBSITE",
        "name": "Website"
      },
      {
        "id": "PAID_ADS",
        "name": "Paid Advertisements"
      },
      {
        "id": "ORGANIC",
        "name": "Organic Search"
      }
    ]
  }
}
```

## 🎯 Opportunity Filtering (In-Filtration)

### Submit Filtered Opportunity
```http
POST /opportunity/inFilter/submit
Cookie: authToken=<jwt_token>
Content-Type: application/json

{
  "opportunityId": "opp_id",
  "action": "ASSIGN_TO_SALES",
  "salesPersonId": "sales_person_id",
  "destinations": ["Kashmir"],
  "priority": "HIGH",
  "notes": "High-value lead from website"
}
```

### Get Filtration Queue
```http
GET /opportunity/inFilter?status=PENDING&limit=20
Cookie: authToken=<jwt_token>
```

## 🌐 Webhook Endpoints

### Lead Capture Webhook
```http
POST /webhook/lead-capture
Content-Type: application/json
X-Webhook-Source: website

{
  "name": "John Doe",
  "email": "john@example.com",
  "phone": "+919876543210",
  "destination": "Kashmir",
  "source": "WEBSITE",
  "utm_source": "google",
  "utm_medium": "cpc"
}
```

## 🔧 Administrative

### Health Check
```http
GET /health
```

**Response:**
```json
{
  "status": "OK",
  "timestamp": "2024-01-15T10:30:00Z"
}
```

### System Status
```http
GET /admin/status
Cookie: authToken=<admin_jwt_token>
```

## 📋 Error Handling

### Standard Error Response
```json
{
  "status_code": 400,
  "message": "Validation failed",
  "error": {
    "code": "VALIDATION_ERROR",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format"
      }
    ]
  }
}
```

### HTTP Status Codes
- `200`: Success
- `201`: Created
- `400`: Bad Request / Validation Error
- `401`: Unauthorized
- `403`: Forbidden
- `404`: Not Found
- `429`: Rate Limited
- `500`: Internal Server Error

## 🔒 Rate Limiting

Rate limits are applied per IP address:
- **General APIs**: 100 requests/minute
- **Authentication**: 5 requests/minute
- **Webhook endpoints**: 1000 requests/minute

Rate limit headers:
```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1642234567
```

## 📝 Request/Response Examples

### Postman Collection
A Postman collection is available with all API endpoints configured with:
- Environment variables for different stages
- Pre-request scripts for authentication
- Test assertions for response validation

### cURL Examples

**Login:**
```bash
curl -X POST http://localhost:3000/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"password123"}'
```

**Get opportunities with authentication:**
```bash
curl -X GET http://localhost:3000/crm/opportunity \
  -H "Content-Type: application/json" \
  -b "authToken=YOUR_JWT_TOKEN"
```

## 🔍 API Testing

### Integration Tests
API endpoints are covered by integration tests in `__tests__/` directory:
- Authentication flows
- CRUD operations
- Error scenarios
- Rate limiting
- Input validation

### Manual Testing
For manual API testing:
1. Start the development server: `npm run dev`
2. Use Postman, Insomnia, or curl
3. Import the provided Postman collection
4. Set up environment variables for different stages
