# Online-quiz-
Frontend – React.js

App.jsx

import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import Login from './pages/Login';
import Register from './pages/Register';
import Dashboard from './pages/Dashboard';
import Quiz from './pages/Quiz';
import Navbar from './components/Navbar';

function App() {
  return (
    <Router>
      <Navbar />
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/login" element={<Login />} />
        <Route path="/register" element={<Register />} />
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/quiz/:id" element={<Quiz />} />
      </Routes>
    </Router>
  );
}
export default App;



Navbar.jsx

import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import Login from './pages/Login';
import Register from './pages/Register';
import Dashboard from './pages/Dashboard';
import Quiz from './pages/Quiz';
import Navbar from './components/Navbar';

function App() {
  return (
    <Router>
      <Navbar />
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/login" element={<Login />} />
        <Route path="/register" element={<Register />} />
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/quiz/:id" element={<Quiz />} />
      </Routes>
    </Router>
  );
}
export default App;

Dashboard.jsx

import { useEffect, useState } from 'react';
import axios from 'axios';
import QuizCard from '../components/QuizCard';

const Dashboard = () =>{
  const [quizzes, setQuizzes] = useState([]);

  useEffect(() =>{
    axios.get('http://localhost:5000/api/quizzes')
      .then(res => setQuizzes(res.data))
      .catch(err => console.error(err));
  }, []);

  return (
    <div className="p-6">
      <h2 className="text-2xl font-bold mb-4">Available Quizzes</h2>
      {quizzes.length === 0 ? (
        <p>No quizzes available right now.</p>
      ) : (
        <div className="grid grid-cols-3 gap-4">
          {quizzes.map(q =><QuizCard key={q._id} quiz={q} />)}
        </div>
      )}
    </div>
  );
};
export default Dashboard;


Backend – Node.js&Express.js
index.js
require('dotenv').config();
const express = require('express');
const cors = require('cors');
const connectDB = require('./config/db');

const app = express();
app.use(express.json());
app.use(cors());

// Connect MongoDB
connectDB();

// Routes
app.get('/', (req, res) =>res.send('API running...'));
app.use('/api/auth', require('./routes/auth'));
app.use('/api/quizzes', require('./routes/quizzes'));

const PORT = process.env.PORT || 5000;
app.listen(PORT, () =>console.log(`🚀 Server started on port ${PORT}`));

db.js
const mongoose = require('mongoose');
const connectDB = async () =>{
  try {
    await mongoose.connect(process.env.MONGO_URI);
    console.log('✅ MongoDB Connected');
  } catch (err) {
    console.error('❌ MongoDB connection failed:', err.message);
    process.exit(1);
  }
};
module.exports = connectDB;

Quiz.js
const mongoose = require('mongoose');

const quizSchema = new mongoose.Schema({
  title: { type: String, required: true },
  description: String,
  category: String,
  questions: [
    {
      text: String,
      options: [String],
      correctIndex: Number
    }
  ]
});

module.exports = mongoose.model('Quiz', quizSchema);

quizzes.js
const express = require('express');
const Quiz = require('../models/Quiz');
const router = express.Router();

// Get all quizzes
router.get('/', async (req, res) =>{
  try {
    const quizzes = await Quiz.find();
    res.status(200).json(quizzes);
  } catch {
    res.status(500).json({ msg: 'Error fetching quizzes' });
  }
});

// Create quiz
router.post('/', async (req, res) =>{
  try {
    const quiz = await Quiz.create(req.body);
    res.status(201).json(quiz);
  } catch {
    res.status(500).json({ msg: 'Error creating quiz' });
  }
});

module.exports = router;
