
Business Requirements Specification: Multi-Vendor E-Commerce & Logistics System


1. Project Overview & Scope

This document outlines the functional requirements and business rules needed to design the relational database for a multi-vendor e-commerce platform with integrated delivery (similar to Amazon Marketplace, Jumia, or Glovo).
The main goal is to build a clean, normalized, and reliable database schema. It needs to connect Customers, Vendors (independent sellers), and Couriers throughout the entire order process, from listing a product in a store to delivering the package at the customer's door.

2. Key Technical & Business Challenges

Running a multi-vendor marketplace brings specific data challenges that this database must handle cleanly:
• Multiple Shops & Product Options: One vendor can run several storefronts. Each product can also come in different variations (like size, color, or storage capacity), with each variation tracking its own price and inventory count.
• Splitting Multi-Vendor Orders: A customer might check out with items from three different shops in one cart. The system must split that single order into separate shipments so each vendor and courier can fulfill their part independently.
• Preserving Historical Records: Product prices and user addresses change over time. However, past invoices and order records must keep the exact price and delivery address used when the purchase happened.
• Preventing Overselling: When multiple customers buy the last item at the exact same time, inventory counts must never drop below zero.
• Step-by-Step Delivery Tracking: Every package needs clear status updates from an assigned courier until it reaches the customer.

3. Functional Requirements by Role


A. User Accounts & Profiles

• Account registration, login, and profile management (first name, last name, unique email, phone number, hashed password, and account status).
• Users can save multiple shipping or billing addresses.
• Role management for Customers, Vendors, Couriers, and Admins.

B. Vendor Management (Shops & Catalog)

• Vendors can create and manage one or more storefronts (shop name, business registration number, description, and average rating).
• Products are organized into a nested category tree (main categories and subcategories).
• Vendors create base product listings tied to specific Product Variants (each with a unique SKU, unit price, stock quantity, and attributes like size or color).

C. Customer Experience (Shopping & Reviews)

• Browsing and filtering products by category, price range, or specific shop.
• A persistent shopping cart that saves items across sessions.
• Checkout flow allowing customers to pick a saved delivery address and choose a payment method (such as Credit Card or Cash on Delivery).
• Live shipment tracking from preparation to doorstep delivery.
• Verified customer reviews with a 1-to-5-star rating system.

D. Delivery & Logistics

• Assigning individual shipments to available couriers (tracking vehicle type, license plate, and active delivery zone).
• Logging granular shipment status updates (Preparing, Picked Up, In Transit, Delivered, Failed Delivery).

4. Business Rules (Data Modeling Foundation)

These rules define the tables, relationships, and cardinalities for the Conceptual Data Model (CDM / MCD) and Logical Data Model (LDM / MLD):
• BR01 (Users & Roles): Each user has one account linked to a unique email address. A base user record specializes into a Customer, Vendor, or Courier profile (table inheritance / sub-typing).
• BR02 (Addresses): A user can save 1 or many addresses (1,n). Each address belongs to only one user (1,1).
• BR03 (Shops): A vendor can operate 1 or many shops (1,n). Each shop is owned by only one vendor (1,1).
• BR04 (Categories): Every product belongs to strictly one category (1,1). A category can hold 0 or many products (0,n) and may link to a parent category (self-referencing relationship).
• BR05 (Products & Variants): Every base product must have at least 1 purchasable variant/SKU (1,n), with each variant storing its own price and inventory count.
• BR06 (Orders): A customer can place 0 or many orders (0,n). Each order belongs to strictly one customer (1,1) and ships to strictly one address (1,1).
• BR07 (Order Items & Price Freezing): An order includes at least 1 product variant (1,n). The junction table (Order_Item) must save the ordered quantity and the unit price at checkout so future catalog edits never change past invoices.
• BR08 (Payments): An order generates 1 or many payment records or attempts (1,n). Each payment ties back to strictly one order (1,1).
• BR09 (Shipments & Couriers): A confirmed order splits into 1 or many shipments based on how many vendors are involved (1,n). Each shipment is assigned to strictly one courier (1,1), while a courier can handle 0 or many shipments (0,n).
• BR10 (Verified Reviews): A customer can post 0 or many reviews (0,n), restricted strictly to products they actually bought and received (enforced via database constraint or trigger). Customers can leave only one review per product.
• BR11 (Inventory Control): Ordered quantities cannot exceed available variant stock (CHECK (stock >= 0)). Confirming an order automatically subtracts the purchased quantity from stock using a database trigger.
