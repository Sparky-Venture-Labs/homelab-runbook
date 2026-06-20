# Network — Runbook App

## Express Security Stack (Helmet + Rate Limit)

```bash
// server/index.ts — Security middleware setup
import helmet from 'helmet';
import rateLimit from 'express-rate-limit';

// Helmet adds HTTP security headers:
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'", "'unsafe-inline'"],
      styleSrc: ["'self'", "'unsafe-inline'"]
    }
  },
  hsts: { maxAge: 31536000, includeSubDomains: true },
  frameguard: { action: 'deny' },
  xssFilter: true
}));

// Rate limiting:
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 300,
  message: 'Too many requests'
});
app.use('/api', limiter);

// Auth middleware for all /api/* routes:
app.use('/api', (req, res, next) => {
  const key = req.headers['x-api-key'];
  if (key !== process.env.API_SECRET) {
    return res.status(401).json({ error: 'Unauthorized' });
  }
  next();
});
```

---
