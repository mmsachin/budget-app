# Code Architecture and Communication Flow

## 1. Frontend Components Structure

### App.js (Root Component)
```javascript
function App() {
  const [isAuthenticated, setIsAuthenticated] = useState(false);
  
  return (
    <div className="min-h-screen bg-gray-100">
      {!isAuthenticated ? (
        <Login onLogin={setIsAuthenticated} />
      ) : (
        <EmployeeManagement />
      )}
    </div>
  );
}
```
- Root component that manages authentication state
- Uses state to switch between Login and EmployeeManagement components
- Once authenticated, shows the EmployeeManagement component

### Login.js
```javascript
const Login = ({ onLogin }) => {
  const handleSubmit = (e) => {
    e.preventDefault();
    if (passcode === 'IKnowYou241202') {
      onLogin(true);  // Sets authentication state in App.js
    }
  };
}
```
- Handles user authentication with a simple passcode
- Uses prop callback to update parent's authentication state

### EmployeeManagement.js (Main Interface)
```javascript
const EmployeeManagement = () => {
  // State management
  const [employees, setEmployees] = useState([]);
  const [formData, setFormData] = useState({
    ldap_id: '',
    name: '',
    role: '',
    manager: ''
  });

  // API Communications
  const fetchEmployees = async () => {
    const response = await fetch('[BACKEND_URL]/api/employees');
    const data = await response.json();
    setEmployees(data);
  };

  const handleSubmit = async (e) => {
    const response = await fetch('[BACKEND_URL]/api/employees', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(formData)
    });
  };
}
```

## 2. Backend API Structure (FastAPI)

### Main API Routes
```python
@app.get("/api/employees")
async def get_employees():
    with database.snapshot() as snapshot:
        results = snapshot.execute_sql(
            "SELECT ldap_id, name, role, manager FROM employees"
        )
        return [{"ldap_id": row[0], ...} for row in results]

@app.post("/api/employees")
async def create_employee(employee: Employee):
    with database.batch() as batch:
        batch.insert(
            table="employees",
            columns=("ldap_id", "name", "role", "manager", ...),
            values=[(employee.ldap_id, ...)]
        )
```

## 3. Communication Flow

### A. Employee Creation Flow
1. **Frontend Initiates**:
   ```javascript
   // User fills form and submits
   const handleSubmit = async (e) => {
     e.preventDefault();
     // Send POST request to backend
     await fetch('/api/employees', {
       method: 'POST',
       body: JSON.stringify(formData)
     });
   }
   ```

2. **Backend Processes**:
   ```python
   @app.post("/api/employees")
   async def create_employee(employee: Employee):
       # Validate data using Pydantic model
       # Insert into Spanner database
       # Return success response
   ```

3. **Frontend Updates**:
   ```javascript
   if (response.ok) {
     // Refresh employee list
     fetchEmployees();
     // Reset form
     setFormData({ ldap_id: '', ... });
   }
   ```

### B. Employee Fetching Flow
1. **Frontend Loads**:
   ```javascript
   useEffect(() => {
     fetchEmployees();  // Runs on component mount
   }, []);
   ```

2. **Backend Retrieves**:
   ```python
   @app.get("/api/employees")
   async def get_employees():
       # Query Spanner database
       # Return employee list
   ```

3. **Frontend Displays**:
   ```javascript
   {employees.map((employee) => (
     <tr key={employee.ldap_id}>
       <td>{employee.name}</td>
       ...
     </tr>
   ))}
   ```

## 4. Data Flow Architecture
```
[React Frontend] 
      ↕️ HTTP/JSON
[FastAPI Backend]
      ↕️ Spanner Client
[Google Cloud Spanner]
```

## 5. Error Handling

### Frontend Error Handling
```javascript
try {
  const response = await fetch('/api/employees');
  if (!response.ok) {
    const errorData = await response.json();
    console.error('Server error:', errorData);
    // Show user-friendly error message
  }
} catch (error) {
  console.error('Network error:', error);
}
```

### Backend Error Handling
```python
@app.post("/api/employees")
async def create_employee(employee: Employee):
    try:
        # Database operations
    except Exception as e:
        raise HTTPException(status_code=400, detail=str(e))
```

## 6. State Management
- Uses React's built-in useState for local state
- Updates state after successful API calls
- Maintains employee list in frontend state
- Auto-refreshes data after modifications

## 7. Security Considerations
- All API requests include CORS headers
- Input validation on both frontend and backend
- FastAPI automatic request/response validation
- Spanner handles data persistence securely

## 8. Performance Features
- Batch database operations
- Optimized database queries with indexes
- Frontend state management for quick rendering
- Efficient re-rendering with React's virtual DOM
