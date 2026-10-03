FROM node:18-alpine

WORKDIR /app/adminService

# 'adminService/' என்ற பாதையைச் சேர்க்கவும்
COPY adminService/package*.json ./
RUN npm install --production

COPY adminService/. ./

ENV NODE_ENV=production
EXPOSE 3003

CMD ["npm", "start"]


