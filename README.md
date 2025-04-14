const express = require('express');
const { createProxyMiddleware } = require('http-proxy-middleware');

const app = express();

// Replace [env] with the actual environment like dev, sit, etc.
const TARGET = 'https://exits-bankwide-search-dev.edi01-apps.dev-pcf.lb4.rbsgrp.net';

app.use('/groupwidesearch', createProxyMiddleware({
  target: TARGET,
  changeOrigin: true,
  pathRewrite: {
    '^/groupwidesearch': '/groupwidesearch',
  },
  onProxyReq: (proxyReq, req, res) => {
    console.log(`[Proxy] ${req.method} ${req.originalUrl}`);
  },
}));

app.listen(4000, () => {
  console.log('🚀 Proxy server is running at http://localhost:4000');
});
