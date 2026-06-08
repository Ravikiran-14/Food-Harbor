# 🔌 Food Harbor - API Documentation

## Complete API Reference

This document provides detailed information about all API endpoints available in the Food Harbor application.

---

## 📋 Table of Contents

- [Base URL](#base-url)
- [Authentication](#authentication)
- [Status Codes](#status-codes)
- [Request/Response Format](#requestresponse-format)
- [Authentication Endpoints](#authentication-endpoints)
- [Customer Endpoints](#customer-endpoints)
- [Restaurant Admin Endpoints](#restaurant-admin-endpoints)
- [Delivery Person Endpoints](#delivery-person-endpoints)
- [Payment Endpoints](#payment-endpoints)
- [Error Handling](#error-handling)

---

## Base URL

```
http://localhost:8080/food-harbor/api
https://yourdomain.com/api (Production)
```

---

## Authentication

All endpoints (except `/auth/register` and `/auth/login`) require authentication via:

### Session Token
```
Cookie: JSESSIONID=<session_id>
```

### JWT Token (Optional)
```
Authorization: Bearer <jwt_token>
```

---

## Status Codes

| Code | Meaning | Description |
|------|---------|-------------|
| 200 | OK | Request successful |
| 201 | Created | Resource created successfully |
| 400 | Bad Request | Invalid request parameters |
| 401 | Unauthorized | Authentication required |
| 403 | Forbidden | Access denied |
| 404 | Not Found | Resource not found |
| 409 | Conflict | Resource already exists |
| 500 | Server Error | Internal server error |

---

## Request/Response Format

### Request Headers
```json
{
  "Content-Type": "application/json",
  "Accept": "application/json"
}
```

### Response Format
```json
{
  "status": "success|error",
  "code": 200,
  "message": "Response message",
  "data": {}
}
```

---

## Authentication Endpoints

### Register User
**POST** `/auth/register`

Register a new user account.

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "SecurePassword@123",
  "fullName": "John Doe",
  "phone": "9876543210",
  "role": "CUSTOMER" // CUSTOMER, RESTAURANT_ADMIN, DELIVERY_PERSON
}
```

**Response:**
```json
{
  "status": "success",
  "code": 201,
  "message": "User registered successfully. Please verify your email.",
  "data": {
    "userId": 1,
    "email": "user@example.com",
    "role": "CUSTOMER"
  }
}
```

**Error Response:**
```json
{
  "status": "error",
  "code": 409,
  "message": "Email already registered"
}
```

---

### Login
**POST** `/auth/login`

Authenticate user and create session.

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "SecurePassword@123"
}
```

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "message": "Login successful",
  "data": {
    "userId": 1,
    "email": "user@example.com",
    "fullName": "John Doe",
    "role": "CUSTOMER",
    "sessionId": "abc123xyz789"
  }
}
```

---

### Logout
**POST** `/auth/logout`

Terminate user session.

**Headers:**
```
Cookie: JSESSIONID=<session_id>
```

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "message": "Logout successful"
}
```

---

### Forgot Password
**POST** `/auth/forgot-password`

Request password reset link.

**Request Body:**
```json
{
  "email": "user@example.com"
}
```

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "message": "Password reset link sent to your email"
}
```

---

### Reset Password
**POST** `/auth/reset-password`

Reset password using token.

**Request Body:**
```json
{
  "token": "reset_token_123",
  "newPassword": "NewPassword@123",
  "confirmPassword": "NewPassword@123"
}
```

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "message": "Password reset successfully"
}
```

---

## Customer Endpoints

### Get Customer Dashboard
**GET** `/customer/dashboard`

Retrieve customer dashboard data.

**Headers:**
```
Cookie: JSESSIONID=<session_id>
```

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "user": {
      "userId": 1,
      "fullName": "John Doe",
      "email": "john@example.com",
      "phone": "9876543210"
    },
    "recentOrders": [
      {
        "orderId": 101,
        "orderNumber": "ORD-001",
        "restaurantName": "Pizza Palace",
        "totalAmount": 450.00,
        "status": "DELIVERED",
        "orderDate": "2026-06-05T14:30:00Z"
      }
    ],
    "savedAddresses": [
      {
        "addressId": 1,
        "label": "Home",
        "street": "123 Main St",
        "city": "Bangalore",
        "state": "Karnataka",
        "zipCode": "560001",
        "isDefault": true
      }
    ]
  }
}
```

---

### List All Restaurants
**GET** `/customer/restaurants?cuisine=italian&rating=4&sortBy=rating`

Get list of restaurants with filtering options.

**Query Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| cuisine | string | Filter by cuisine type |
| rating | number | Minimum rating filter (1-5) |
| city | string | Filter by city |
| sortBy | string | Sort by: rating, distance, delivery_time |
| page | number | Page number (default: 1) |
| limit | number | Results per page (default: 10) |

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "total": 45,
    "page": 1,
    "restaurants": [
      {
        "restaurantId": 1,
        "name": "Pizza Palace",
        "description": "Authentic Italian pizzas",
        "cuisineType": "Italian",
        "address": "456 Pizza Lane, Bangalore",
        "phone": "9123456789",
        "rating": 4.5,
        "reviewCount": 234,
        "deliveryTime": "30-45 mins",
        "deliveryFee": 50,
        "minOrderValue": 200,
        "isOpen": true
      }
    ]
  }
}
```

---

### Get Restaurant Menu
**GET** `/customer/menu/{restaurantId}`

Get menu items for a specific restaurant.

**Path Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| restaurantId | number | Restaurant ID |

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "restaurantId": 1,
    "restaurantName": "Pizza Palace",
    "categories": [
      {
        "categoryId": 1,
        "name": "Pizzas",
        "items": [
          {
            "itemId": 101,
            "name": "Margherita",
            "description": "Classic tomato and mozzarella",
            "price": 250,
            "image": "url_to_image",
            "isVegetarian": true,
            "isAvailable": true,
            "preparationTime": 15
          }
        ]
      }
    ]
  }
}
```

---

### Add Item to Cart
**POST** `/customer/cart/add`

Add food item to shopping cart.

**Request Body:**
```json
{
  "itemId": 101,
  "quantity": 2,
  "restaurantId": 1,
  "specialInstructions": "Extra cheese, no onions"
}
```

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "cartId": 5,
    "items": [
      {
        "cartItemId": 1,
        "itemId": 101,
        "itemName": "Margherita",
        "quantity": 2,
        "unitPrice": 250,
        "subtotal": 500,
        "specialInstructions": "Extra cheese, no onions"
      }
    ],
    "subtotal": 500,
    "taxes": 50,
    "deliveryFee": 50,
    "total": 600
  }
}
```

---

### Remove Item from Cart
**DELETE** `/customer/cart/remove/{cartItemId}`

Remove item from cart.

**Path Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| cartItemId | number | Cart item ID |

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "message": "Item removed from cart",
  "data": {
    "cartId": 5,
    "total": 500
  }
}
```

---

### Place Order
**POST** `/customer/order/place`

Create new food order.

**Request Body:**
```json
{
  "restaurantId": 1,
  "deliveryAddressId": 1,
  "paymentMethod": "CREDIT_CARD",
  "promoCode": "SAVE50",
  "specialInstructions": "Deliver after 5 PM"
}
```

**Response:**
```json
{
  "status": "success",
  "code": 201,
  "message": "Order placed successfully",
  "data": {
    "orderId": 101,
    "orderNumber": "ORD-2026-001",
    "restaurantId": 1,
    "restaurantName": "Pizza Palace",
    "totalAmount": 550,
    "status": "CONFIRMED",
    "estimatedDeliveryTime": "2026-06-08T15:30:00Z",
    "orderDate": "2026-06-08T15:00:00Z"
  }
}
```

---

### Get Order History
**GET** `/customer/orders?status=ALL&page=1&limit=10`

Retrieve customer's order history.

**Query Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| status | string | ALL, PENDING, CONFIRMED, PREPARING, OUT_FOR_DELIVERY, DELIVERED, CANCELLED |
| page | number | Page number |
| limit | number | Results per page |

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "total": 12,
    "page": 1,
    "orders": [
      {
        "orderId": 101,
        "orderNumber": "ORD-2026-001",
        "restaurantId": 1,
        "restaurantName": "Pizza Palace",
        "orderDate": "2026-06-05T14:30:00Z",
        "totalAmount": 550,
        "status": "DELIVERED",
        "deliveryAddress": "123 Main St, Bangalore",
        "items": [
          {
            "itemId": 101,
            "itemName": "Margherita",
            "quantity": 2,
            "price": 250
          }
        ]
      }
    ]
  }
}
```

---

### Get Order Tracking
**GET** `/customer/order/{orderId}/tracking`

Get real-time order status and delivery tracking.

**Path Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| orderId | number | Order ID |

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "orderId": 101,
    "orderNumber": "ORD-2026-001",
    "currentStatus": "OUT_FOR_DELIVERY",
    "timeline": [
      {
        "status": "CONFIRMED",
        "timestamp": "2026-06-08T15:00:00Z",
        "description": "Order confirmed by restaurant"
      },
      {
        "status": "PREPARING",
        "timestamp": "2026-06-08T15:05:00Z",
        "description": "Chef is preparing your order"
      },
      {
        "status": "READY_FOR_PICKUP",
        "timestamp": "2026-06-08T15:20:00Z",
        "description": "Order ready for delivery"
      },
      {
        "status": "OUT_FOR_DELIVERY",
        "timestamp": "2026-06-08T15:25:00Z",
        "description": "Delivery partner picked up your order"
      }
    ],
    "deliveryPartner": {
      "deliveryPersonId": 5,
      "name": "Raj Kumar",
      "phone": "9999888777",
      "rating": 4.8,
      "vehicleNumber": "KA-01-AB-1234",
      "currentLocation": {
        "latitude": 12.9352,
        "longitude": 77.6245
      }
    },
    "estimatedDeliveryTime": "2026-06-08T15:45:00Z"
  }
}
```

---

### Submit Rating & Review
**POST** `/customer/rating/submit`

Submit restaurant rating and review.

**Request Body:**
```json
{
  "orderId": 101,
  "restaurantId": 1,
  "rating": 4,
  "review": "Great food, quick delivery!"
}
```

**Response:**
```json
{
  "status": "success",
  "code": 201,
  "message": "Rating submitted successfully",
  "data": {
    "ratingId": 201,
    "restaurantId": 1,
    "rating": 4,
    "review": "Great food, quick delivery!",
    "createdAt": "2026-06-08T16:00:00Z"
  }
}
```

---

## Restaurant Admin Endpoints

### Get Admin Dashboard
**GET** `/admin/dashboard`

Retrieve restaurant admin dashboard data.

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "restaurant": {
      "restaurantId": 1,
      "name": "Pizza Palace",
      "cuisineType": "Italian",
      "rating": 4.5
    },
    "todayStats": {
      "totalOrders": 45,
      "totalRevenue": 22500,
      "avgOrderValue": 500,
      "completedOrders": 42,
      "pendingOrders": 3
    },
    "weekStats": {
      "totalOrders": 320,
      "totalRevenue": 160000,
      "topDish": "Margherita Pizza"
    },
    "pendingOrders": [
      {
        "orderId": 102,
        "orderNumber": "ORD-2026-002",
        "customerName": "Jane Doe",
        "totalAmount": 750,
        "status": "CONFIRMED",
        "orderTime": "2026-06-08T16:30:00Z"
      }
    ]
  }
}
```

---

### Get Restaurant Details
**GET** `/admin/restaurant`

Retrieve restaurant information.

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "restaurantId": 1,
    "name": "Pizza Palace",
    "description": "Authentic Italian cuisine",
    "cuisineType": "Italian",
    "address": "456 Pizza Lane, Bangalore",
    "phone": "9123456789",
    "email": "contact@pizzapalace.com",
    "rating": 4.5,
    "openingTime": "11:00",
    "closingTime": "23:00",
    "isOpen": true,
    "deliveryFee": 50,
    "minOrderValue": 200,
    "image": "url_to_image"
  }
}
```

---

### Update Restaurant Details
**PUT** `/admin/restaurant/update`

Update restaurant information.

**Request Body:**
```json
{
  "name": "Pizza Palace",
  "description": "Updated description",
  "phone": "9123456789",
  "openingTime": "11:00",
  "closingTime": "23:00",
  "deliveryFee": 60,
  "minOrderValue": 250
}
```

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "message": "Restaurant updated successfully",
  "data": {
    "restaurantId": 1,
    "name": "Pizza Palace",
    "updatedAt": "2026-06-08T17:00:00Z"
  }
}
```

---

### Get Menu Items
**GET** `/admin/menu?category=Pizzas&page=1&limit=20`

Retrieve restaurant menu items.

**Query Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| category | string | Filter by category |
| page | number | Page number |
| limit | number | Results per page |

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "total": 50,
    "items": [
      {
        "itemId": 101,
        "name": "Margherita",
        "description": "Classic tomato and mozzarella",
        "category": "Pizzas",
        "price": 250,
        "preparationTime": 15,
        "isVegetarian": true,
        "isAvailable": true,
        "image": "url_to_image"
      }
    ]
  }
}
```

---

### Add Menu Item
**POST** `/admin/menu/add`

Add new food item to menu.

**Request Body:**
```json
{
  "name": "Margherita",
  "description": "Classic tomato and mozzarella",
  "category": "Pizzas",
  "price": 250,
  "preparationTime": 15,
  "isVegetarian": true,
  "image": "image_url_or_base64"
}
```

**Response:**
```json
{
  "status": "success",
  "code": 201,
  "message": "Menu item added successfully",
  "data": {
    "itemId": 101,
    "name": "Margherita",
    "price": 250
  }
}
```

---

### Update Menu Item
**PUT** `/admin/menu/update/{itemId}`

Update existing menu item.

**Path Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| itemId | number | Item ID |

**Request Body:**
```json
{
  "name": "Margherita",
  "description": "Updated description",
  "price": 280,
  "isAvailable": true
}
```

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "message": "Menu item updated successfully",
  "data": {
    "itemId": 101,
    "name": "Margherita",
    "price": 280
  }
}
```

---

### Delete Menu Item
**DELETE** `/admin/menu/delete/{itemId}`

Remove menu item.

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "message": "Menu item deleted successfully"
}
```

---

### Manage Orders
**GET** `/admin/orders?status=PENDING&page=1&limit=20`

Get orders for restaurant.

**Query Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| status | string | Filter by status |
| page | number | Page number |
| limit | number | Results per page |

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "total": 15,
    "orders": [
      {
        "orderId": 102,
        "orderNumber": "ORD-2026-002",
        "customerName": "Jane Doe",
        "totalAmount": 750,
        "status": "CONFIRMED",
        "items": [
          {
            "itemName": "Margherita",
            "quantity": 2,
            "price": 250
          }
        ],
        "orderTime": "2026-06-08T16:30:00Z"
      }
    ]
  }
}
```

---

### Update Order Status
**PUT** `/admin/order/status/{orderId}`

Update order status.

**Path Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| orderId | number | Order ID |

**Request Body:**
```json
{
  "status": "PREPARING"
}
```

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "message": "Order status updated successfully",
  "data": {
    "orderId": 102,
    "status": "PREPARING",
    "updatedAt": "2026-06-08T16:35:00Z"
  }
}
```

---

### Get Analytics
**GET** `/admin/analytics?period=weekly`

Retrieve sales and performance analytics.

**Query Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| period | string | daily, weekly, monthly |

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "period": "weekly",
    "totalOrders": 320,
    "totalRevenue": 160000,
    "avgOrderValue": 500,
    "topDishes": [
      {
        "itemName": "Margherita",
        "quantity": 120,
        "revenue": 30000
      }
    ],
    "chartData": {
      "dates": ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"],
      "orders": [40, 45, 50, 48, 52, 55, 30],
      "revenue": [20000, 22500, 25000, 24000, 26000, 27500, 15000]
    }
  }
}
```

---

## Delivery Person Endpoints

### Get Delivery Dashboard
**GET** `/delivery/dashboard`

Retrieve delivery person dashboard.

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "deliveryPerson": {
      "deliveryPersonId": 5,
      "name": "Raj Kumar",
      "phone": "9999888777",
      "vehicleNumber": "KA-01-AB-1234",
      "rating": 4.8
    },
    "todayStats": {
      "completedDeliveries": 12,
      "totalEarnings": 1200,
      "averageRating": 4.8,
      "ongoingDeliveries": 2
    },
    "ongoingDeliveries": [
      {
        "deliveryId": 501,
        "orderId": 102,
        "customerName": "Jane Doe",
        "pickupLocation": "Pizza Palace, 456 Pizza Lane",
        "deliveryLocation": "123 Main St, Bangalore",
        "status": "PICKED_UP"
      }
    ]
  }
}
```

---

### Get Active Deliveries
**GET** `/delivery/active`

Get list of active deliveries assigned.

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "activeDeliveries": [
      {
        "deliveryId": 501,
        "orderId": 102,
        "orderNumber": "ORD-2026-002",
        "customerName": "Jane Doe",
        "customerPhone": "9876543211",
        "restaurantName": "Pizza Palace",
        "pickupAddress": "456 Pizza Lane, Bangalore",
        "deliveryAddress": "123 Main St, Bangalore",
        "distance": 5.2,
        "estimatedTime": 15,
        "totalAmount": 750,
        "status": "ASSIGNED"
      }
    ]
  }
}
```

---

### Update Delivery Status
**PUT** `/delivery/status/{deliveryId}`

Update delivery status.

**Path Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| deliveryId | number | Delivery ID |

**Request Body:**
```json
{
  "status": "DELIVERED",
  "currentLocation": {
    "latitude": 12.9352,
    "longitude": 77.6245
  }
}
```

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "message": "Delivery status updated",
  "data": {
    "deliveryId": 501,
    "status": "DELIVERED",
    "updatedAt": "2026-06-08T16:45:00Z"
  }
}
```

---

### Get Delivery History
**GET** `/delivery/history?page=1&limit=20`

Retrieve delivery history.

**Query Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| page | number | Page number |
| limit | number | Results per page |

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "total": 145,
    "deliveries": [
      {
        "deliveryId": 501,
        "orderId": 102,
        "orderNumber": "ORD-2026-002",
        "customerName": "Jane Doe",
        "restaurantName": "Pizza Palace",
        "totalAmount": 750,
        "status": "DELIVERED",
        "rating": 5,
        "deliveredAt": "2026-06-08T16:45:00Z"
      }
    ]
  }
}
```

---

## Payment Endpoints

### Process Payment
**POST** `/payment/process`

Process payment for order.

**Request Body:**
```json
{
  "orderId": 102,
  "amount": 750,
  "paymentMethod": "CREDIT_CARD",
  "cardDetails": {
    "cardNumber": "4111111111111111",
    "cardholderName": "John Doe",
    "expiryMonth": 12,
    "expiryYear": 2025,
    "cvv": "123"
  }
}
```

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "message": "Payment processed successfully",
  "data": {
    "paymentId": 301,
    "orderId": 102,
    "amount": 750,
    "status": "SUCCESS",
    "transactionId": "TXN-2026-001",
    "paymentDate": "2026-06-08T15:00:00Z"
  }
}
```

---

### Verify Payment Status
**GET** `/payment/verify/{paymentId}`

Verify payment status.

**Path Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| paymentId | number | Payment ID |

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "paymentId": 301,
    "orderId": 102,
    "amount": 750,
    "paymentStatus": "SUCCESS",
    "transactionId": "TXN-2026-001",
    "paymentMethod": "CREDIT_CARD",
    "timestamp": "2026-06-08T15:00:00Z"
  }
}
```

---

### Get Payment Receipt
**GET** `/payment/receipt/{paymentId}`

Download payment receipt.

**Response:**
```json
{
  "status": "success",
  "code": 200,
  "data": {
    "receiptUrl": "https://yourdomain.com/receipts/REC-2026-001.pdf",
    "receiptNumber": "REC-2026-001",
    "orderId": 102,
    "amount": 750,
    "paymentDate": "2026-06-08T15:00:00Z"
  }
}
```

---

## Error Handling

### Error Response Format

```json
{
  "status": "error",
  "code": 400,
  "message": "Error message",
  "errors": [
    {
      "field": "email",
      "message": "Invalid email format"
    }
  ]
}
```

### Common Errors

#### Validation Error (400)
```json
{
  "status": "error",
  "code": 400,
  "message": "Validation failed",
  "errors": [
    {
      "field": "email",
      "message": "Email is required"
    },
    {
      "field": "password",
      "message": "Password must be at least 8 characters"
    }
  ]
}
```

#### Unauthorized (401)
```json
{
  "status": "error",
  "code": 401,
  "message": "Please login to continue"
}
```

#### Forbidden (403)
```json
{
  "status": "error",
  "code": 403,
  "message": "You don't have permission to access this resource"
}
```

#### Not Found (404)
```json
{
  "status": "error",
  "code": 404,
  "message": "Resource not found"
}
```

#### Conflict (409)
```json
{
  "status": "error",
  "code": 409,
  "message": "Email already registered"
}
```

#### Server Error (500)
```json
{
  "status": "error",
  "code": 500,
  "message": "Internal server error. Please try again later."
}
```

---

## Rate Limiting

API endpoints are rate-limited to prevent abuse:

- **Authentication endpoints**: 5 requests per minute per IP
- **Public endpoints**: 30 requests per minute per IP
- **Authenticated endpoints**: 60 requests per minute per user

---

## Best Practices

1. **Always validate input** on the client side
2. **Use HTTPS** for all API calls in production
3. **Handle errors gracefully** with appropriate error messages
4. **Implement retry logic** for failed requests
5. **Cache responses** where appropriate
6. **Monitor API usage** and implement rate limiting
7. **Keep sensitive data secure** (passwords, card numbers, etc.)

---

**Last Updated**: June 2026
**API Version**: 1.0
