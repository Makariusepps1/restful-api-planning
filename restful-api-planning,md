# RESTful API Planning — NHTSA vPIC Client

## 1. API Selection

**API chosen:** NHTSA Vehicle API (vPIC — Vehicle Product Information Catalog)
**Base URL:** `https://vpic.nhtsa.dot.gov/api/vehicles/`

**Purpose:** vPIC is the National Highway Traffic Safety Administration's public catalog of vehicle data. It exposes vehicle variables (what a vehicle can have — airbags, braking systems, lighting) and decodes Vehicle Identification Numbers (VINs) into structured vehicle details.

**Why it is suitable:** it is free, requires no API key, is stable, and returns simple, well-structured JSON. I have already verified it live: `GetVehicleVariableList` returns 145 variables, 11 of which belong to the "Active Safety System" group. The response shape is a `Results` array of objects with `ID`, `Name`, `Description`, and `GroupName`, which maps cleanly onto a table UI and a form dropdown.

**REST principles:** resources are identified by URIs — each variable list and each VIN is its own resource (`/GetVehicleVariableList`, `/DecodeVin/{vin}`). The API is stateless: every request carries everything it needs, with no session or login. It uses standard HTTP methods — GET for all reads. Representation is negotiated with `?format=json`; the default is XML, so the format parameter is how the client asks for JSON.

## 2. Endpoint Mapping

| Endpoint | Method | Returns / Accepts |
|---|---|---|
| `/api/vehicles/GetVehicleVariableList?format=json` | GET | Returns all vehicle variables: `ID`, `Name`, `Description`, `GroupName`. Used to populate the variable table and the form dropdown. |
| `/api/vehicles/GetVehicleVariableValuesList/{variableName}?format=json` | GET | Returns the allowed values for one variable. Used when a user selects a variable and wants its possible values. |
| `/api/vehicles/DecodeVin/{vin}?format=json` | GET | Returns decoded fields for a VIN as a `Results` array of `Variable` / `Value` pairs. Used by the VIN lookup. |
| `http://127.0.0.1:5000/vehicles/` (own Python backend — contract only) | POST | Accepts JSON `{ "vin", "variable_name", "note" }`. The backend validates it with Pydantic, stores it in SQLite and a JSON Lines file, and returns `201` with the created record. |
| `http://127.0.0.1:5000/vehicles/` (own Python backend — contract only) | GET | Returns all saved vehicle notes from the database. |

GET is read-only and safe — it never changes anything on the server. POST creates a resource on the backend. Each endpoint addresses one resource with one verb, which is what keeps the interface predictable.

## 3. Data Flow Diagram (textual)

**Variable list flow:**

