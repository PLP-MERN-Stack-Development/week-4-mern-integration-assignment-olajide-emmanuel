// =======================
// 📁 backend/.env
// =======================
/*
PORT=5000
MONGO_URI=mongodb://localhost:27017/mernDB
*/

// =======================
// 📁 backend/config/db.js
// =======================
const mongoose = require('mongoose');

const connectDB = async () => {
  try {
    await mongoose.connect(process.env.MONGO_URI, {
      useNewUrlParser: true,
      useUnifiedTopology: true,
    });
    console.log('✅ MongoDB Connected');
  } catch (error) {
    console.error('❌ MongoDB connection error:', error.message);
    process.exit(1);
  }
};

module.exports = connectDB;

// =======================
// 📁 backend/models/User.js
// =======================
const mongoose = require('mongoose');

const UserSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
  },
  email: {
    type: String,
    required: true,
    unique: true,
  },
  password: {
    type: String,
    required: true,
  },
});

module.exports = mongoose.model('User', UserSchema);

// =======================
// 📁 backend/controllers/userController.js
// =======================
const User = require('../models/User');

const getUsers = async (req, res) => {
  try {
    const users = await User.find();
    res.json(users);
  } catch (err) {
    res.status(500).json({ message: err.message });
  }
};

const createUser = async (req, res) => {
  try {
    const { name, email, password } = req.body;
    const user = new User({ name, email, password });
    await user.save();
    res.status(201).json(user);
  } catch (err) {
    res.status(400).json({ message: err.message });
  }
};

module.exports = { getUsers, createUser };

// =======================
// 📁 backend/routes/userRoutes.js
// =======================
const express = require('express');
const router = express.Router();
const { getUsers, createUser } = require('../controllers/userController');

router.get('/', getUsers);
router.post('/', createUser);

module.exports = router;

// =======================
// 📁 backend/server.js
// =======================
const express = require('express');
const cors = require('cors');
const dotenv = require('dotenv');
const connectDB = require('./config/db');
const userRoutes = require('./routes/userRoutes');

dotenv.config();
connectDB();

const app = express();

app.use(cors());
app.use(express.json());

app.use('/api/users', userRoutes);

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`🚀 Server running on port ${PORT}`));

// =======================
// 📁 frontend/.env
// =======================
/*
VITE_API_BASE=/api
*/

// =======================
// 📁 frontend/vite.config.js
// =======================
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:5000',
        changeOrigin: true,
        secure: false,
      },
    },
  },
});

// =======================
// 📁 frontend/src/main.jsx
// =======================
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

// =======================
// 📁 frontend/src/App.jsx
// =======================
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import Home from './pages/Home';

function App() {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<Home />} />
      </Routes>
    </Router>
  );
}

export default App;

// =======================
// 📁 frontend/src/pages/Home.jsx
// =======================
import React, { useEffect, useState } from 'react';
import axios from 'axios';

function Home() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    axios
      .get(import.meta.env.VITE_API_BASE + '/users')
      .then((res) => setUsers(res.data))
      .catch((err) => console.error(err));
  }, []);

  return (
    <div className="p-4">
      <h1>User List</h1>
      {users.map((user, index) => (
        <div key={index}>
          <strong>{user.name}</strong> - {user.email}
        </div>
      ))}
    </div>
  );
}

export default Home;


// =======================
// 📁 backend/.env
// =======================
/*
PORT=5000
MONGO_URI=mongodb://localhost:27017/blogDB
*/

// =======================
// 📁 backend/config/db.js
// =======================
const mongoose = require('mongoose');

const connectDB = async () => {
  try {
    await mongoose.connect(process.env.MONGO_URI, {
      useNewUrlParser: true,
      useUnifiedTopology: true,
    });
    console.log('✅ MongoDB Connected');
  } catch (error) {
    console.error('❌ MongoDB connection error:', error.message);
    process.exit(1);
  }
};

module.exports = connectDB;

// =======================
// 📁 backend/models/Category.js
// =======================
const mongoose = require('mongoose');

const CategorySchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
    unique: true,
  },
});

module.exports = mongoose.model('Category', CategorySchema);

// =======================
// 📁 backend/models/Post.js
// =======================
const mongoose = require('mongoose');

const PostSchema = new mongoose.Schema({
  title: {
    type: String,
    required: true,
  },
  content: {
    type: String,
    required: true,
  },
  category: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Category',
    required: true,
  },
}, { timestamps: true });

module.exports = mongoose.model('Post', PostSchema);

// =======================
// 📁 backend/middleware/validate.js
// =======================
const { validationResult } = require('express-validator');

const validateRequest = (req, res, next) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({ errors: errors.array() });
  }
  next();
};

module.exports = validateRequest;

// =======================
// 📁 backend/controllers/postController.js
// =======================
const Post = require('../models/Post');

const getAllPosts = async (req, res) => {
  const posts = await Post.find().populate('category', 'name');
  res.json(posts);
};

const getPostById = async (req, res) => {
  const post = await Post.findById(req.params.id).populate('category', 'name');
  if (!post) return res.status(404).json({ message: 'Post not found' });
  res.json(post);
};

const createPost = async (req, res) => {
  const { title, content, category } = req.body;
  const post = new Post({ title, content, category });
  await post.save();
  res.status(201).json(post);
};

const updatePost = async (req, r



// =======================
// 📁 frontend/src/App.jsx
// =======================
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import Navbar from './components/Navbar';
import Home from './pages/Home';
import PostDetail from './pages/PostDetail';
import PostForm from './pages/PostForm';

function App() {
  return (
    <Router>
      <Navbar />
      <div className="container">
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/posts/:id" element={<PostDetail />} />
          <Route path="/create" element={<PostForm />} />
          <Route path="/edit/:id" element={<PostForm />} />
        </Routes>
      </div>
    </Router>
  );
}

export default App;

// =======================
// 📁 frontend/src/components/Navbar.jsx
// =======================
import { Link } from 'react-router-dom';

const Navbar = () => (
  <nav style={{ padding: '10px', borderBottom: '1px solid #ccc' }}>
    <Link to="/" style={{ marginRight: '10px' }}>Home</Link>
    <Link to="/create">Create Post</Link>
  </nav>
);

export default Navbar;

// =======================
// 📁 frontend/src/pages/Home.jsx
// =======================
import { useEffect, useState } from 'react';
import { Link } from 'react-router-dom';
import useApi from '../utils/useApi';

const Home = () => {
  const [posts, setPosts] = useState([]);
  const api = useApi();

  useEffect(() => {
    api.get('/posts')
      .then(res => setPosts(res.data))
      .catch(err => console.error(err));
  }, []);

  return (
    <div>
      <h2>All Blog Posts</h2>
      {posts.map(post => (
        <div key={post._id}>
          <Link to={`/posts/${post._id}`}>
            <h3>{post.title}</h3>
          </Link>
          <p>Category: {post.category?.name || 'N/A'}</p>
        </div>
      ))}
    </div>
  );
};

export default Home;

// =======================
// 📁 frontend/src/pages/PostDetail.jsx
// =======================
import { useParams, Link, useNavigate } from 'react-router-dom';
import { useEffect, useState } from 'react';
import useApi from '../utils/useApi';

const PostDetail = () => {
  const { id } = useParams();
  const api = useApi();
  const navigate = useNavigate();
  const [post, setPost] = useState(null);

  useEffect(() => {
    api.get(`/posts/${id}`).then(res => setPost(res.data));
  }, [id]);

  const handleDelete = async () => {
    await api.delete(`/posts/${id}`);
    navigate('/');
  };

  if (!post) return <p>Loading...</p>;

  return (
    <div>
      <h2>{post.title}</h2>
      <p>{post.content}</p>
      <p><strong>Category:</strong> {post.category?.name}</p>
      <Link to={`/edit/${post._id}`}>Edit</Link>
      <button onClick={handleDelete} style={{ marginLeft: '10px' }}>Delete</button>
    </div>
  );
};

export default PostDetail;

// =======================
// 📁 frontend/src/pages/PostForm.jsx
// =======================
import { useEffect, useState } from 'react';
import { useNavigate, useParams } from 'react-router-dom';
import useApi from '../utils/useApi';

const PostForm = () => {
  const api = useApi();
  const navigate = useNavigate();
  const { id } = useParams();
  const isEdit = Boolean(id);

  const [title, setTitle] = useState('');
  const [content, setContent] = useState('');
  const [category, setCategory] = useState('');
  const [categories, setCategories] = useState([]);

  useEffect(() => {
    api.get('/categories').then(res => setCategories(res.data));
    if (isEdit) {
      api.get(`/posts/${id}`).then(res => {
        setTitle(res.data.title);
        setContent(res.data.content);
        setCategory(res.data.category);
      });
    }
  }, [id]);

  const handleSubmit = async (e) => {
    e.preventDefault();
    const data = { title, content, category };
    if (isEdit) {
      await api.put(`/posts/${id}`, data);
    } else {
      await api.post('/posts', data);
    }
    navigate('/');
  };

  return (
    <div>
      <h2>{isEdit ? 'Edit Post' : 'Create Post'}</h2>
      <form onSubmit={handleSubmit}>
        <input
          value={title}
          onChange={(e) => setTitle(e.target.value)}
          placeholder="Title"
          required
        /><br />
        <textarea
          value={content}
          onChange={(e) => setContent(e.target.value)}
          placeholder="Content"
          required
        /><br />
        <select value={category} onChange={(e) => setCategory(e.target.value)} required>
          <option value="">-- Select Category --</option>
          {categories.map(cat => (
            <option key={cat._id} value={cat._id}>{cat.name}</option>
          ))}
        </select><br />
        <button type="submit">{isEdit ? 'Update' : 'Create'}</button>
      </form>
    </div>
  );
};

export default PostForm;

// =======================
// 📁 frontend/src/utils/useApi.js
// =======================
import axios from 'axios';

const useApi = () => {
  const instance = axios.create({
    baseURL: '/api',
  });

  return instance;
};

export default useApi;

// =======================
// 📁 src/context/BlogContext.jsx
// =======================
import { createContext, useContext, useEffect, useState } from 'react';
import useApi from '../utils/useApi';

const BlogContext = createContext();

export const useBlog = () => useContext(BlogContext);

export const BlogProvider = ({ children }) => {
  const api = useApi();
  const [posts, setPosts] = useState([]);
  const [categories, setCategories] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  // Fetch posts and categories on load
  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        const [postRes, catRes] = await Promise.all([
          api.get('/posts'),
          api.get('/categories'),
        ]);
        setPosts(postRes.data);
        setCategories(catRes.data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
    fetchData();
  }, []);

  // Optimistic Add Post
  const addPost = async (postData) => {
    try {
      const tempPost = { ...postData, _id: Date.now().toString(), optimistic: true };
      setPosts(prev => [tempPost, ...prev]);
      const res = await api.post('/posts', postData);
      setPosts(prev => [res.data, ...prev.filter(p => p._id !== tempPost._id)]);
    } catch (err) {
      setError('Failed to create post.');
    }
  };

  // Optimistic Delete Post
  const deletePost = async (id) => {
    const prevPosts = [...posts];
    setPosts(posts.filter(p => p._id !== id));
    try {
      await api.delete(`/posts/${id}`);
    } catch (err) {
      setPosts(prevPosts); // Rollback
      setError('Failed to delete post.');
    }
  };

  // Update Post
  const updatePost = async (id, data) => {
    try {
      const res = await api.put(`/posts/${id}`, data);
      setPosts(posts.map(p => (p._id === id ? res.data : p)));
    } catch (err) {
      setError('Failed to update post.');
    }
  };

  return (
    <BlogContext.Provider value={{
      posts, categories, loading, error,
      addPost, deletePost, updatePost,
    }}>
      {children}
    </BlogContext.Provider>
  );
};


// =======================
// 📁 .env
// =======================
/*
PORT=5000
MONGO_URI=mongodb://localhost:27017/blogDB
JWT_SECRET=your_jwt_secret
*/

// =======================
// 📁 server.js
// =======================
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
const dotenv = require('dotenv');
const authRoutes = require('./routes/authRoutes');
const postRoutes = require('./routes/postRoutes');
const commentRoutes = require('./routes/commentRoutes');
const path = require('path');

dotenv.config();
const app = express();

mongoose.connect(process.env.MONGO_URI, {
  useNewUrlParser: true,
  useUnifiedTopology: true,
}).then(() => console.log('MongoDB Connected'));

app.use(cors());
app.use(express.json());
app.use('/uploads', express.static(path.join(__dirname, 'uploads')));

app.use('/api/auth', authRoutes);
app.use('/api/posts', postRoutes);
app.use('/api/posts', commentRoutes);

app.listen(process.env.PORT, () => console.log(`Server running on port ${process.env.PORT}`));

// =======================
// 📁 models/User.js
// =======================
const mongoose = require('mongoose');
const bcrypt = require('bcryptjs');

const UserSchema = new mongoose.Schema({
  username: { type: String, required: true, unique: true },
  email: { type: String, required: true, unique: true },
  password: { type: String, required: true },
});

UserSchema.pre('save', async function () {
  if (!this.isModified('password')) return;
  this.password = await bcrypt.hash(this.password, 10);
});

UserSchema.methods.matchPassword = async function (entered) {
  return await bcrypt.compare(entered, this.password);
};

module.exports = mongoose.model('User', UserSchema);

// =======================
// 📁 middleware/protect.js
// =======================
const jwt = require('jsonwebtoken');
const User = require('../models/User');

const protect = async (req, res, next) => {
  try {
    const token = req.headers.authorization?.split(' ')[1];
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = await User.findById(decoded.id).select('-password');
    next();
  } catch {
    res.status(401).json({ message: 'Not authorized' });
  }
};

module.exports = protect;

// =======================
// 📁 routes/authRoutes.js
// =======================
const express = require('express');
const jwt = require('jsonwebtoken');
const User = require('../models/User');
const router = express.Router();

router.post('/register', async (req, res) => {
  const { username, email, password } = req.body;
  const user = await User.create({ username, email, password });
  const token = jwt.sign({ id: user._id }, process.env.JWT_SECRET);
  res.json({ user: { username, email }, token });
});

router.post('/login', async (req, res) => {
  const { email, password } = req.body;
  const user = await User.findOne({ email });
  if (!user || !(await user.matchPassword(password)))
    return res.status(401).json({ message: 'Invalid credentials' });
  const token = jwt.sign({ id: user._id }, process.env.JWT_SECRET);
  res.json({ user: { username: user.username, email }, token });
});

module.exports = router;

// =======================
// 📁 models/Post.js
// =======================
const mongoose = require('mongoose');

const PostSchema = new mongoose.Schema({
  title: String,
  content: String,
  image: String,
  category: String,
}, { timestamps: true });

module.exports = mongoose.model('Post', PostSchema);

// =======================
// 📁 models/Comment.js
// =======================
const mongoose = require('mongoose');

const CommentSchema = new mongoose.Schema({
  postId: { type: mongoose.Schema.Types.ObjectId, ref: 'Post' },
  author: String,
  content: String,
}, { timestamps: true });

module.exports = mongoose.model('Comment', CommentSchema);

// =======================
// 📁 middleware/upload.js
// =======================
const multer = require('multer');
const path = require('path');

const storage = multer.diskStorage({
  destination: (req, file, cb) => cb(null, 'uploads/'),
  filename: (req, file, cb) => cb(null, `${Date.now()}-${file.originalname}`),
});

module.exports = multer({ storage });

// =======================
// 📁 routes/postRoutes.js
// =======================
const express = require('express');
const Post = require('../models/Post');
const protect = require('../middleware/protect');
const upload = require('../middleware/upload');
const router = express.Router();

router.get('/', async (req, res) => {
  const { keyword, category, page = 1 } = req.query;
  const limit = 5;
  const skip = (page - 1) * limit;
  const query = {};
  if (keyword) query.title = { $regex: keyword, $options: 'i' };
  if (category) query.category = category;
  const total = await Post.countDocuments(query);
  const posts = await Post.find(query).skip(skip).limit(limit);
  res.json({ posts, total, page: Number(page), pages: Math.ceil(total / limit) });
});

router.post('/', protect, upload.single('image'), async (req, res) => {
  const imageUrl = req.file ? `/uploads/${req.file.filename}` : '';
  const post = await Post.create({ ...req.body, image: imageUrl });
  res.status(201).json(post);
});

router.put('/:id', protect, async (req, res) => {
  const post = await Post.findByIdAndUpdate(req.params.id, req.body, { new: true });
  res.json(post);
});

router.delete('/:id', protect, async (req, res) => {
  await Post.findByIdAndDelete(req.params.id);
  res.json({ message: 'Post deleted' });
});

module.exports = router;

// =======================
// 📁 routes/commentRoutes.js
// =======================
const express = require('express');
const Comment = require('../models/Comment');
const router = express.Router();

router.get('/:postId/comments', async (req, res) => {
  const comments = await Comment.find({ postId: req.params.postId });
  res.json(comments);
});

router.post('/:postId/comments', async (req, res) => {
  const comment = await Comment.create({
    postId: req.params.postId,
    author: req.body.author,
    content: req.body.content,
  });
  res.status(201).json(comment);
});

module.exports = router;




