# Backend Requirement Specifications for Airbnb Clone


### Validation Rules
- **Email** must be unique and in valid format.
- **Password** must be at least 8 characters, include one uppercase letter, one number, and one special character.
- **Name** required, min length: 2.


### Performance Criteria
- Registration and login responses < 300ms under normal load.
- Tokens must expire after configurable timeout (default: 15 minutes for access, 7 days for refresh).
- Support at least 1,000 concurrent login requests.


---


## 2. Property Management


### Description
Enables hosts to add, update, view, and delete property listings.


### API Endpoints
- **POST /api/properties**
- Input: `{ name, description, location, pricePerNight, images[] }`
- Output: `{ propertyId, hostId, name, description, location, pricePerNight, createdAt }`
- **GET /api/properties/{id}**
- Output: `{ propertyId, hostId, name, description, location, pricePerNight, images[], createdAt }`
- **PUT /api/properties/{id}**
- Input: `{ name?, description?, location?, pricePerNight?, images[]? }`
- Output: `{ propertyId, updatedFields, updatedAt }`
- **DELETE /api/properties/{id}**
- Output: `{ message: "Property deleted successfully" }`
- **GET /api/properties?location=&priceRange=&date=**
- Output: `[ { propertyId, name, location, pricePerNight, availability } ]`


### Validation Rules
- **Name**: required, max length 100.
- **Description**: required, max length 1000.
- **Location**: required, must be valid string.
- **PricePerNight**: required, must be >= 0.
- **Images**: optional, but if provided, must be valid URLs.


### Performance Criteria
- Property search must return results in < 500ms for queries up to 100,000 listings.
- API must handle at least 500 property CRUD operations per minute.
- Pagination supported for search results (default 20 per page).


---


## 3. Booking System


### Description
Handles customer reservations for properties, ensuring availability and secure transaction flow.


### API Endpoints
- **POST /api/bookings**
- Input: `{ propertyId, customerId, checkInDate, checkOutDate, paymentMethod }`
- Output: `{ bookingId, propertyId, customerId, checkInDate, checkOutDate, status, totalAmount, createdAt }`
- **GET /api/bookings/{id}**
- Output: `{ bookingId, propertyId, customerId, checkInDate, checkOutDate, status, totalAmount, createdAt }`
- **GET /api/bookings?customerId=**
- Output: `[ { bookingId, propertyId, checkInDate, checkOutDate, status } ]`
- **DELETE /api/bookings/{id}**
- Output: `{ message: "Booking canceled successfully" }`


### Validation Rules
- **propertyId** must exist in Property Database.
- **customerId** must exist in User Database.
- **checkInDate** < **checkOutDate**.
- Dates must be in ISO 8601 format.
- No overlapping bookings allowed for the same property.


### Performance Criteria
- Booking confirmation < 400ms under normal load.
- Handle concurrent booking requests without double-booking (atomic transactions).
- Support at least 200 bookings per minute during peak times.


---


## General Notes
- All endpoints require JWT-based authentication (except `/auth/register` and `/auth/login`).
- Error responses follow standard format: `{ errorCode, message, details? }`.
- Rate limiting: 100 requests per minute per user.