# AI-Driven Local Food Waste Reduction and Donation Coordination System

This workflow automates the process of managing local food donations, optimizing allocations using AI, and notifying recipients to reduce food waste effectively.

---

## Workflow Overview

The system consists of two main processes:

1. **Real-time Donation Handling**
2. **Batch Processing of Donations Near Expiration**

---

## Nodes and Workflow Steps

### 1. Receive Donation Data  
- **Type:** HTTP Trigger  
- **Trigger:** HTTP POST request at `/register-donation`  
- **Purpose:** Accepts incoming food donation details including donor type, donor name, food description, quantity, expiration date, pickup location, and contact info.

### 2. Store Donation  
- **Type:** PostgreSQL Insert  
- **Table:** `donations`  
- **Stored Data:** Donor and donation details along with current timestamp (`timestamp`).

### 3. AI Optimize Allocation  
- **Type:** HTTP Request  
- **Request to:** `http://ai-optimization-service.local/optimize`  
- **Payload:** Individual donation details including donation ID, food description, quantity, expiration date, and pickup location.  
- **Purpose:** Calls an AI optimization service that calculates optimal food distribution destinations and quantities.

### 4. Save Allocation  
- **Type:** PostgreSQL Insert  
- **Table:** `allocations`  
- **Stored Data:** Optimized allocation data including donation ID, destination type & name, contact info, allocated quantity, and scheduled pickup time.

### 5. Send Notification Email  
- **Type:** Email Send  
- **Purpose:** Sends an email notification to the allocated recipient with details about the donation item, quantity, pickup location, time, and donor contact info.

---

## Batch Processing of Donations About to Expire

### 6. Get Donations About to Expire  
- **Type:** PostgreSQL Select  
- **Filter:** Donations with expiration dates within the next 24 hours.

### 7. Batch AI Optimization  
- **Type:** HTTP Request  
- **Request to:** `http://ai-optimization-service.local/optimize-batch`  
- **Payload:** List of donations about to expire for bulk optimization.

### 8. Save Batch Allocations  
- **Type:** PostgreSQL Insert  
- **Table:** `allocations`  
- **Stored Data:** Bulk allocation details for all optimized allocations returned from the batch AI service.

### 9. Prepare Notification Data  
- **Type:** Function  
- **Purpose:** Transforms batch allocation data into a format suitable for email notifications, preparing recipient contact info and donation details.

### 10. Send Batch Notification  
- **Type:** Email Send  
- **Purpose:** Sends batch email notifications to recipients about their allocation of food donations, including relevant item and pickup details.

---

## Database Tables

### `donations`  
| Column          | Description                         |  
|-----------------|-----------------------------------|  
| donorType       | Type of donor (e.g., restaurant)  |  
| donorName       | Name of donor                      |  
| foodDescription | Description of the donated item    |  
| quantity        | Amount of food donated             |  
| expirationDate  | Expiry date of the food            |  
| pickupLocation  | Location to pick up the donation   |  
| contactInfo     | Contact details of the donor       |  
| timestamp       | Timestamp when the donation was recorded |

### `allocations`  
| Column           | Description                                    |  
|------------------|------------------------------------------------|  
| donationId       | Reference to donation record                    |  
| destinationType  | Recipient type (e.g., shelter, food bank)      |  
| destinationName  | Recipient organization or individual's name    |  
| contactInfo     | Recipient contact information                    |  
| allocatedQuantity| Quantity of food allocated                       |  
| pickupTime      | Scheduled pickup time                            |

---

## Credentials Required

- **PostgreSQL:** Named `LocalPostgresDB` for database operations.  
- **SMTP:** Named `LocalSMTP` for sending notification emails.

---

## Email Notifications

- Sent from: `no-reply@localfoodwaste.org`  
- Subject lines:
  - Real-time: "New Food Donation Allocation Notification"  
  - Batch: "Upcoming Food Donation Allocation"  
- Emails include item description, quantity, pickup location/time, and donor contact info.

---

## Summary

This workflow streamlines local food waste reduction by:

- Receiving and storing donation data efficiently.
- Leveraging AI services for optimized allocation of food donations.
- Sending timely email notifications to ensure smooth pickup and distribution.
- Handling urgent donations nearing expiration through batch processing to minimize waste effectively.

---

End of workflow documentation.