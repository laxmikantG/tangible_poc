
# Tangible - Surgical Instrument Set Recognition System

### Description
  Demo a prototype system to check presence or absence of instruments in a surgical set.
  At this stage however, the client only wants to see that the system can perform the core functionalities as above. They
  want it as simple as possible, but it must work effectively.

### In-Scope
  * Develop a web application to find out prsence or absence of instruments in a surgical set.
  * Prepare computer vision/deep learning model to predict the prsence or absence of instruments in a surgical set
  * The final system will have all the requisite modules:
      1. User Registration and Permissions
      2. System Setup
      3. Register Sets
      4. Recognise Sets
      5. Train Model System
      6. Scan Sets for Completeness
      7. MRP & Inventory Management System
      8. Billing System
  * for PoC purposes, we will only focus on 1 to 6

### Out of  scope
    * MRP & Inventory Management System
    * Billing System
    * Anything that is not defined in In-Scope

### Understanding/Assumptions
   The SYNTHES® Modular Hand System provides implants and instruments specifically designed for fractures, replantations and                reconstruction of the hand.
   
  ![Figure1 - A Modular Hand System](https://i.imgur.com/Nz53u6c.png)
  
  * Modular Hand Systems Modules are also referred as Sets 
  * Modular Hand System provides Modules for 1.0mm/1.3mm, 1.5mm, 2.0mm, and 2.4mm implants
  * Each Set consists of differrent modules consisting of various tools such as Screws, Plates, 
    Instruments, Tray instruments and Addition (We will refer 'instrument' for each of them as a generic term)  
  * Each instrument comes in various dimensions as per the Phalanges and Carpals
  
  * Available Instruments 
     - Screws
        * Screws in the Modular Hand System
        * Self-tapping flutes
        * Low profile screw head
     - Plates
        * T-Plates
        * Straight Plates
        * Straight LC-DC Plates
        * Extended H-Plates
        * Y-Plates
        * Condylar Plates/a 1.8mm Buttress Pin for the Condylar Plate 2.4
     - Instruments
        * Drill bits
        * countersinks
        * screwdriver shafts
     * These instruments are colour-coded for easy recognition to correspond to the screw size.
     - Tray Instruments 
        * Bending and Cutting Pliers
        * Elevators and Retractors
        * Universal Drill Guides
        * Double Drill Sleeves
        * Depth Gauges
        * Screwdrivers
        * Forceps
     - Additional SYNTHES® products for treatment of the wrist and hand:
        * Wrist Fusion Plate
        * 3.0mm Cannulated Screw with Threaded Washer
        * Distal Radius Plate
     - Separate Image has been provided for each instrument belongs to Modular Hand System Modules
       along with their dimensions and quantity in set.
     - Also Includes items for DPW (DPW = Don’t Pack Without.) i.e Dont pack the set without these instruments.


### Architecture Diagrams

  1. Technical Architecture
  2. Client-Server Technology Stack 
  3. ML Model Pipe line.
  **TODO: - Diagram needs to be cleaned**
  
  ![Imgur](https://i.imgur.com/6QA7enn.jpg)
  
  4. User Flow Diagram (Yet to prepare)
  

### DB Tables 
  As User Story suggests,
  - End User will login to the System, So User-Registration is required
  - He will register Patients/Cases, So patients db is required 
  - For each cases, there could be multiple Sets - Set Registration is required 
  - Each Set has multiple instruments, so separate table for instruments is required
  - A Table to record user tests and their results, which can be used to achieve more accuracy in future.

##### Table definitions:

- Django User Model (as well as Groups to manage user hierarchy if required )

- Patients/Cases Registration - Table Name - Patients
  Fields - Type - Description 
  1. case_id - Text - A unique uuid/guid for each case
  2. case - Text - Information about patient/case/surgery etc.
  3. Sets  - FK_Sets - One to Many (With Django lets use Many to Many for a clean UI)  
  4. sets_count - Integer - Number of Sets
  Optional
  <!-- 5. patient_name - Text - Name of the patient (Patient should be a separate table, but not required as of now)
  6. Age - Text - Age of the patient
  7. Gender - Boolean/Integer -  Patient Gender -->


- Sets (Modular set)
  Fields - Type - Description 
  1. set_id - Text - A unique uuid/guid for each set
  2. set_name - Text - A set name 
  3. description - Text - Description about the set
  4. instruments  - FK_Instrument - One to Many (With Django lets use Many to Many for a clean UI)
  5. qunatity - Integer - FK_Instrument_quantity
  6. instruction - Text - Instuctions about setting up the partiicular set
  7. Captured Image Path (img_link) - Text/ImageField - to store Images of the given set

- Instruments 
  Fields - Type - Description 
  1. Instrument ID - Text - A unique Instrument ID 
  2. name - Text -  Instrument Name 
  3. Type - Text -  Instrument Type 
  4. DPW - Boolean/Int - Dont pack without - Yes/No
  5. Catalog No. - Text - Catalog Number 
  6. Description - Text - Other required details.
  7. Captured Image Path (img_link) - Text/ImageField - to store Images of the given instrument


- Recognition Cases 
  Fields - Type - Description 
  1. case_id - text - A unique ID for Recognition
  2. recognised - boolean - Recognised or not
  3. image_path - uploaded image path
  4. Errors/Issues - Text - Details about the errors or issue if any
  5. Recognised_Set - FK_Set - If recognised 
  6. Recognised_Instrument - FK_Instrument - If recognized

- Each table will contain follwing extra attributes 
  1. created - Created DateTime stamp in UTC
  2. modified - modified DateTime Stamp in UTC

# Routes

|Type |ROUTE | METHOD | PARAMS| RESPONSE| DESCRIPTION|
| ---- | ----  |----   | ----| ----| ----|
|**Users** |/users | GET   | - | Returns users.html with list of uesrs |  List all users |
||/users/create/| GET| - | Returns ui for Create User Template | Creates User in the database|
||/users/create/| POST|First Name, Last Name, Email, Password | Returns ui for Register User Template  | User getting created in the system|
||/users/edit/ | GET| - |   Returns ui for Edit User Template | Renders the UI to edit User details|
||/users/edit/ | POST| First Name, Last Name, Password |   Returns ui for Edit User Template | Allows to edit User details|  
||/users/delete/| POST| Email | Deletes the User from AJAX on /users ui | Deletes the user from the system|
| **Cases**|/cases/|GET| - | Returns cases.html with list of all cases| Lists all the available cases|
| | /case/new/ |GET| Case Title, Multi-ple Options to select the Sets  | Returns new_case.html containing fields for PARAMS| Renders the HTML for new case creation|
| | /case/new/ |POST| - | Returns new_case.html containing with result| Creates a new Case in the system |
| | /case/edit/ |GET| - | Returns edit_case.html containing fields for PARAMS| Creates a new Case in the system |
|**Sets** | /sets/|GET|-| List all the available 5 Sets in sets.html | Shows Modular sets available in the system| 
| |/set/ or /Set/register| GET | - | Render a template register_set.html | UI to Register a new set in the System|
| |/set/ or /Set/register| POST | Set name, description, multiple options for instruments, Instructions, image | Show results on register_set.html | Registers a new set in the System|
| |/set/edit| GET | Set name, description, multiple options for instruments, Instructions, image | Returns edit_set.html containing fields for PARAMS | Allows to edit a new set in the System|
| |/set/edit| POST | Set name, description, multiple options for instruments, Instructions, image | Show results on edit_set.html | Edits a set in the System|
|**Instruments** | /instruments/|GET|-| List all the available instruments in instruments.html | Shows Modular set's instruments available in the system| 
| |/instrument/ or /instruments/register| GET | - | Render a template register_instrument.html | UI to Register a new instrument in the System|
| |/instrument/ or /instruments/register| POST | Instru. name, description, Instructions/notes, image | Show results on register_set.html | Registers a new instrument in the System|
| |/instrument/edit| GET |Instru. name, description, Instructions/notes, image | Returns edit_instrument.html containing fields for PARAMS | Allows to edit a instrument in the System|
| |/instrument/edit| POST | Instru. name, description, Instructions/notes, image | Show results on edit_instrument.html | Edits a instrument in the System|



#### Two Main APIs 

|API End Point URL | `/recognise/set/`|
|----|---|
|METHOD  **GET** | Shows the UI to upload a captured image/captures a set image|
|METHOD **POST**| Validates and predicts reesult on given image against trained model and displays the result accordingly on the UI including details of the given Set.|
|PARAMS in POST call| image_set - Captured image of a Modular Set|
|RESPONSE| `{'set_id': <Unique Set Identified>, 'set_name': <Unique Set Name as per the database>, 'case_id': <All the available case ids for the set>, 'description': <Description of the Given Set>, 'message' : 'Relevant Success message such as "Set Identified as - Modular Hand 1.5mm set", etc', 'status': 'Status of the result/ True or False, 'error': 'Error if any' }`|


   If the system identifies correct Set, then it will display each instrument on set.

|API End Point URL | `/recognise/instrument/`|
|----|---|
|METHOD  **GET** | UI Shows the first instrument with rounding box around it along with its details as per the each instrument Validated and predicted on given image against trained model and displays the result accordingly on the UI including details of the given instrument.|
|PARAMS | ```{"instrument_id": "<A Unique Instrument ID>", "instrument_name": "<An Instrument Name>", "description": "<Instrument Description>", "Type": "<Instrument Type>"}```|


### Deployement Architecture/Suggestion

### Deployment Instruction

### Running Tests

### Aknowlwdgements 

### Authors 

### License
