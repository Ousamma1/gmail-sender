const express = require('express');
const bodyParser = require('body-parser');
const nodemailer = require('nodemailer');
const cors = require('cors');
require('dotenv').config();

const app = express();
const PORT = process.env.PORT || 3000;

app.use(cors());
app.use(bodyParser.json());

app.post('/send-email', async (req, res) => {
  const { toEmail, subject, content } = req.body;

  if (!toEmail || !subject || !content) {
    return res.status(400).json({ message: 'Missing required fields' });
  }

  const transporter = nodemailer.createTransport({
    service: 'gmail',
    auth: {
      user: process.env.GMAIL_USER,
      pass: process.env.GMAIL_APP_PASSWORD
    }
  });

  const mailOptions = {
    from: process.env.GMAIL_USER,
    to: toEmail,
    subject: subject,
    text: content
  };

  try {
    const info = await transporter.sendMail(mailOptions);
    res.status(200).json({ message: 'Email sent successfully', info });
  } catch (error) {
    res.status(500).json({ message: 'Email send failed', error: error.message });
  }
});

app.get('/', (req, res) => {
  res.send('Gmail Sender API is running');
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
