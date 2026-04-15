[Technical Requirements Specification_ Industrial Shipment & Inventory Management System (ISM v9.0).md](https://github.com/user-attachments/files/26741546/Technical.Requirements.Specification_.Industrial.Shipment.Inventory.Management.System.ISM.v9.0.md)
### Technical Requirements Specification: Industrial Shipment & Inventory Management System (ISM v9.0)

#### 1\. System Infrastructure and Environmental Constraints

In the context of high-volume industrial logistics, the structural integrity of the hosting environment is as critical as the application logic itself. To ensure maximum operational uptime and mitigate the risks associated with external network volatility, ISM v9.0 is engineered for a localized server deployment. This strategic localization guarantees real-time responsiveness and data availability at the physical point of operation.

##### Technology Stack and Development Protocol

The system architecture is strictly confined to the following stack to ensure long-term maintainability and hardware compatibility:

* **Operating System:**  Linux Ubuntu.  
* **Hardware Platform:**  Raspberry Pi 4 (RPI4).  
* **Logic and Web Tier:**  LAMP Stack (Linux, Apache, MySQL, and a logic layer utilizing PHP/Python).  
* **Version Control:**  All development, branching, and commits must occur exclusively via  **GitHub** .

##### Reliability and Error Handling Protocols

To maintain industrial-grade data integrity, the following technical mandates are in effect:

* **Database Persistence:**  Automated hourly backups of the MySQL environment are mandatory.  
* **Granular Error Reporting:**  The system must implement explicit error handling at the interface level, providing clear, actionable feedback for every failure point.  
* **ACID Compliance:**  The software philosophy transitions from hardware stability to data reliability by enforcing strict transaction protocols, ensuring that no data is lost during system transitions.

#### 2\. Core Development Paradigms and Data Integrity

The high-stakes nature of inventory management necessitates a "Single Source of Truth." Data persistence and rigorous input validation prevent the cascading financial discrepancies often found in legacy logistics systems.

##### Non-Negotiable Development Rules

Rule Category,Requirement Description  
Record Persistence,NEVER DELETE . The system must utilize UPDATE operations exclusively to maintain a permanent audit trail.  
Data Originality,Protect the integrity of initial entries. Only  Status  and  Location  fields are mutable during a record's lifecycle.  
Validation Logic,"Mandatory UI confirmation for all numerical inputs (Weight, Quantity, etc.) prior to database commit."  
User Attribution,"Every transaction must append the performing user's identity to the ""Comments"" field in the specific format:  ""Username Created Now"" (CVS) ."

##### UI/UX Responsiveness and Scalability

* **Hardware Agnostic Design:**  All interfaces must be fully responsive, specifically optimized for high-resolution monitors and legacy mobile devices used by floor personnel.  
* **Specialized Panels:**  The  **Forklift**  and  **Weight Station**  panels must prioritize touch-friendly, high-contrast layouts for mobile efficiency.  
* **Transaction Feedback:**  Every operation must feature a two-stage feedback loop: a data-complete  **Confirmation**  screen before submission and a  **Success/Fail**  notification post-transaction.

#### 3\. Entity Logic: License Formatting and Database Constraints

The system utilizes the  **License Number (Lic Number)**  as the primary key (PK) for shipment tracking. Precise formatting and searchability are non-negotiable for system-wide integration.

##### Farsi-Based License Transformation

To support regional requirements while maintaining a standardized database index, the following transformation logic applies:

* **Storage Primary Key:**  12-FARSI-123-IR12 (e.g., 12-الف-123-IR12).  
* **Display Interface:**  12-123-IR12.  
* **Search Logic:**  Database lookups and "Check for existing lic number" actions must use the concatenated hyphenated format (12-123-الف-IR12).

##### "Add Truck" Entity Requirements

The Add Truck form must capture comprehensive driver and vehicle data. If a license does not exist after a manual "Check," the following fields become mandatory:

1. **License Segment 1:**  2 digits (10-99).  
2. **License Segment 2 (Farsi Letter):**  Dropdown menu (الف, ب, پ, ت).  
3. **License Segment 3:**  3 digits (100-999).  
4. **License Segment 4 (Region):**  2 digits (10-99).  
5. **Driver Details:**  Driver Name, Driver Lic Number, and Driver Phone.  
6. **Metadata:**  Username Created Now (CVS) in comments.  
7. **Initial State:**  Status:  **Free** , Location:  **Entrance** .

#### 4\. Operational Workflows: Incoming and Outgoing Processes

Standardization of workflows ensures that shipment states transition predictably between the Entrance, Weight Station, Warehouse (Anbar), and Office.

##### Incoming and Outgoing Technical Sequences

* **Add/Verify Truck:**  Initial registration or status check.  
* **Add Shipment:**  Creation of the shipment record.  
* *Logic:*  Sets Truck to  **Status: Busy**  and  **Location: Entrance** .  
* *UX:*  The page must  **Reload in 5 SECONDS**  after a success message.  
* **Weight 1:**  Initial weighing at the station.  
* *Transition:*  Sets Shipment to  **Status: Loading Unloading** , Location:  **Weight1** .  
* **Loading/Unloading:**  Execution via the Forklift Panel.  
* **Weight 2:**  Final weighing.  
* *Transition:*  Sets Shipment to  **Status: Office** , Location:  **Office** .  
* **Create Order:**  Generation of Purchase Order (Incoming) or Sales Order (Outgoing).  
* *Transition:*  Sets Truck to  **Status: Free**  and Location to  **Delivered/Entrance** .

#### 5\. Technical Specifications for Master Data Forms

Dynamic frontend logic via AJAX and JavaScript is required to maintain Foreign Key (FK) relationships and prevent orphaned records.

##### Form-Specific Requirements

* **Add Customer/Supplier:**  System must perform a real-time check against existing databases. Fail messages must display: "Already Exists with NAME, Please add FULL NAME and TRY AGAIN."  
* **Add New Units:**  The form must dynamically load  **Suppliers**  and  **Material Types**  as a hierarchical filter before allowing new unit creation (e.g., Count/KG).  
* **Add New Anbar (Warehouse):**  Upon submission of a new "Location Name," the system must trigger a dynamic table creation logic: Anbar\_Location Name. This new table must inherit all standard fields from the master Anbar schema.

#### 6\. Functional Requirements: Weight Station and Forklift Panels

##### Weight Station Panel Logic

* **Physical Constraints:**  All weight inputs must fall within the range of  **9 KG to 38,000 KG** .  
* **Net Weight Verification:**  During the "Update Weight 2" phase, the system must calculate the absolute difference: |Weight 1 \- Weight 2|. The user must manually enter a NET Weight; the system will only commit the record if the manual entry matches the system calculation.

##### Forklift Panel Logic

* **Quantity Transformation:**  For raw materials, entering a "Quantity" (X) during unloading must trigger the creation of  **X individual records**  in the respective Anbar\_ table.  
* **Safety Cap:**  The "Used" operation for raw materials is limited to a  **Quantity Max of 4**  per transaction.  
* **Reel Management:**  "Add New Reel" requires unique Reel Number (auto-suggested next in sequence), Width, GSM, Length,  **Breaks** ,  **Grade** , and a  **Consumption Profile**  dropdown. The interface must include a  **Print QR Code**  button.  
* **Stock Movement:**  The "Moved" logic facilitates stock transfer between Anbars. The system must update the status to "Moved" in the source table and generate new "In-stock" records in the destination Anbar\_ table.

#### 7\. Financial Modules, Administration, and Reporting

##### Purchase and Sales Orders (PO/SO)

Financial modules are accessible only when a shipment reaches "Office" status.

* **Financial Formula:**  Total Price \= Net Weight x Price Per KG x VAT%. (Exclude Extra Cost from this calculation).  
* **Field Constraints:**   **Price Per KG**  must support a  **1,000 separator format**  for Iranian Rial (IRR).  **VAT%**  must be a dropdown (0-10%) with a  **Default of 0** .  
* **Outputs:**  Automated PDF generation and a mandatory Print Pop-up.

##### Report Section and Admin Panel

* **Admin Panel:**  Utilize the standard Django admin interface for comprehensive entity management.  
* **Reporting Requirements:**  Mandatory export formats are  **Excel and PDF** .  
* **Inventory Reports:**  Sorted by  **Width**  for Reels and  **Supplier/Material**  for Raw Materials.  
* **Views:**  Reports must be toggleable between Daily, Weekly, Monthly, and Yearly views.  
* **Development Panel:**  A visualization suite for shipment trends utilizing charts.

#### 8\. Cancel Shipment Module

This specialized module handles the reversion of system states in the event of transaction errors.

* **Search Scope:**  The UI must display the  **last 5 shipments** .  
* **Mandatory Input:**  A "Cancellation Reason" field is required for all actions.  
* **Cascading Updates:**  
* Revert Truck status to  **Free**  and Location to  **Entrance** .  
* Update Purchase/Sales DB entries to  **Canceled** .  
* Revert Reel statuses in the Products and Anbar\_ tables to  **In-stock** .  
* Update Shipment record to  **Status: Canceled** .

#### 9\. Final Technical Mandates

The development team is reminded that  **User Attribution**  (CVS) and  **Farsi License Formatting**  are the core pillars of the system's search and audit capabilities. Adherence to the  **"No Deletion"**  protocol is absolute and must be enforced through database permissions where possible.  
