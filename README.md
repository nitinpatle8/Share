import express from 'express';
import { createProxyMiddleware } from 'http-proxy-middleware';
import services from './services.json' assert { type: 'json' };

const app = express();

console.log('🌍 Proxy server running for multiple environments');

app.use('/:env', (req, res, next) => {
  const { env } = req.params;
  
  // Check if the environment exists (dev, sit, uat, nft)
  if (!['dev', 'sit', 'uat', 'nft'].includes(env)) {
    return res.status(400).send(`Invalid environment: ${env}`);
  }

  // Dynamically handle each service
  Object.entries(services).forEach(([service, urlTemplate]) => {
    const target = urlTemplate.replace('[]', env);

    app.use(`/:env/${service}`, createProxyMiddleware({
      target,
      changeOrigin: true,
      pathRewrite: {
        [`^/:env/${service}`]: '', // Strip the service name and environment prefix
      },
      onProxyReq: (proxyReq, req, res) => {
        console.log(`[Proxy] ${req.method} ${req.originalUrl} → ${target}`);
      }
    }));
  });

  next(); // Proceed with the request
});

app.listen(4000, () => {
  console.log('🚀 Proxy server running at http://localhost:4000');
});
