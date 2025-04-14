import express from 'express';
import { createProxyMiddleware } from 'http-proxy-middleware';
import services from './services.json' assert { type: 'json' };

const app = express();

const ENV = process.env.ENV || 'dev';

console.log(`🌍 Starting proxy for ENV: ${ENV}`);

Object.entries(services).forEach(([service, urlTemplate]) => {
  const target = urlTemplate.replace('[]', ENV);

  app.use(`/${service}`, createProxyMiddleware({
    target,
    changeOrigin: true,
    pathRewrite: {
      [`^/${service}`]: '', // Strip service prefix
    },
    onProxyReq: (proxyReq, req, res) => {
      console.log(`[Proxy] ${req.method} ${req.originalUrl} → ${target}`);
    }
  }));
});

app.listen(4000, () => {
  console.log('🚀 Proxy server running at http://localhost:4000');
});
