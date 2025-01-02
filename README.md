# Calendar-Tracking-App
// src/index.js
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { store } from './redux/store';
import App from './App';
import './index.css';

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);

// src/redux/store.js
import { configureStore } from '@reduxjs/toolkit';
import adminReducer from './slices/adminSlice';
import userReducer from './slices/userSlice';

export const store = configureStore({
  reducer: {
    admin: adminReducer,
    user: userReducer,
  },
});

// src/redux/slices/adminSlice.js
import { createSlice } from '@reduxjs/toolkit';

const adminSlice = createSlice({
  name: 'admin',
  initialState: {
    companies: [],
    communicationMethods: [
      { name: 'LinkedIn Post', description: '', mandatory: true },
      { name: 'LinkedIn Message', description: '', mandatory: true },
      { name: 'Email', description: '', mandatory: true },
      { name: 'Phone Call', description: '', mandatory: false },
      { name: 'Other', description: '', mandatory: false },
    ],
  },
  reducers: {
    addCompany: (state, action) => {
      state.companies.push(action.payload);
    },
    updateCompany: (state, action) => {
      const index = state.companies.findIndex(
        (company) => company.id === action.payload.id
      );
      if (index !== -1) {
        state.companies[index] = action.payload;
      }
    },
    deleteCompany: (state, action) => {
      state.companies = state.companies.filter(
        (company) => company.id !== action.payload
      );
    },
  },
});

export const { addCompany, updateCompany, deleteCompany } = adminSlice.actions;
export default adminSlice.reducer;

// src/redux/slices/userSlice.js
import { createSlice } from '@reduxjs/toolkit';

const userSlice = createSlice({
  name: 'user',
  initialState: {
    communications: [],
  },
  reducers: {
    logCommunication: (state, action) => {
      state.communications.push(action.payload);
    },
    updateCommunication: (state, action) => {
      const index = state.communications.findIndex(
        (comm) => comm.id === action.payload.id
      );
      if (index !== -1) {
        state.communications[index] = action.payload;
      }
    },
  },
});

export const { logCommunication, updateCommunication } = userSlice.actions;
export default userSlice.reducer;

// src/components/Admin/CompanyManagement.js
import React, { useState } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { addCompany, updateCompany, deleteCompany } from '../../redux/slices/adminSlice';

const CompanyManagement = () => {
  const companies = useSelector((state) => state.admin.companies);
  const dispatch = useDispatch();
  const [form, setForm] = useState({
    name: '',
    location: '',
    linkedin: '',
    emails: '',
    phone: '',
    comments: '',
    periodicity: '2 weeks',
  });
  const [editMode, setEditMode] = useState(false);
  const [editId, setEditId] = useState(null);

  const handleSubmit = () => {
    if (form.name) {
      if (editMode) {
        dispatch(updateCompany({ ...form, id: editId }));
        setEditMode(false);
        setEditId(null);
      } else {
        dispatch(addCompany({ ...form, id: Date.now() }));
      }
      setForm({
        name: '',
        location: '',
        linkedin: '',
        emails: '',
        phone: '',
        comments: '',
        periodicity: '2 weeks',
      });
    }
  };

  const handleEdit = (company) => {
    setEditMode(true);
    setEditId(company.id);
    setForm(company);
  };

  const handleDelete = (id) => {
    dispatch(deleteCompany(id));
  };

  return (
    <div>
      <h2>Company Management</h2>
      <form>
        <input
          type="text"
          placeholder="Company Name"
          value={form.name}
          onChange={(e) => setForm({ ...form, name: e.target.value })}
        />
        <input
          type="text"
          placeholder="Location"
          value={form.location}
          onChange={(e) => setForm({ ...form, location: e.target.value })}
        />
        <input
          type="text"
          placeholder="LinkedIn"
          value={form.linkedin}
          onChange={(e) => setForm({ ...form, linkedin: e.target.value })}
        />
        <input
          type="text"
          placeholder="Emails"
          value={form.emails}
          onChange={(e) => setForm({ ...form, emails: e.target.value })}
        />
        <input
          type="text"
          placeholder="Phone"
          value={form.phone}
          onChange={(e) => setForm({ ...form, phone: e.target.value })}
        />
        <textarea
          placeholder="Comments"
          value={form.comments}
          onChange={(e) => setForm({ ...form, comments: e.target.value })}
        />
        <input
          type="text"
          placeholder="Periodicity"
          value={form.periodicity}
          onChange={(e) => setForm({ ...form, periodicity: e.target.value })}
        />
        <button type="button" onClick={handleSubmit}>
          {editMode ? 'Update Company' : 'Add Company'}
        </button>
      </form>
      <ul>
        {companies.map((company) => (
          <li key={company.id}>
            {company.name}
            <button onClick={() => handleEdit(company)}>Edit</button>
            <button onClick={() => handleDelete(company.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
};

export default CompanyManagement;

// src/App.js
import React from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import CompanyManagement from './components/Admin/CompanyManagement';
import Dashboard from './components/User/Dashboard';
import Notifications from './components/User/Notifications';

const App = () => {
  return (
    <Router>
      <Routes>
        <Route path="/admin" element={<CompanyManagement />} />
        <Route path="/user" element={<Dashboard />} />
        <Route path="/notifications" element={<Notifications />} />
      </Routes>
    </Router>
  );
};

export default App;

// Additional components for Notifications, CalendarView, and Reporting modules can be added similarly.

