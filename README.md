require('dotenv').config();
const mongoose = require('mongoose');
const bcrypt = require('bcrypt');
const User = require('../models/User');
const connectDB = require('../config/db');

const seed = async () => {
  await connectDB(process.env.MONGO_URI || 'mongodb://localhost:27017/auth_demo');

  // wipe
  await User.deleteMany({});

  const saltRounds = Number(process.env.BCRYPT_SALT_ROUNDS) || 10;
  const users = [
    { name: 'Alice Admin', email: 'alice@demo.com', password: 'Admin#123', role: 'admin' },
    { name: 'Maya Manager', email: 'maya@demo.com', password: 'Manager#123', role: 'manager' },
    { name: 'Uday User', email: 'uday@demo.com', password: 'User#123', role: 'user' }
  ];

  for (const u of users) {
    const hash = await bcrypt.hash(u.password, saltRounds);
    await User.create({ name: u.name, email: u.email, password: hash, role: u.role });
    console.log(`Seeded ${u.email}`);
  }

  console.log('Seeding complete');
  process.exit(0);
};

seed().catch(err => { console.error(err); process.exit(1); });
