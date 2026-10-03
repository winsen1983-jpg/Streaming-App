FROM node:18-alpine

WORKDIR /app/chatService

# 'chatService/' என்ற பாதையைச் சேர்க்கவும்
COPY chatService/package*.json ./
RUN npm install --production

COPY chatService/. ./

ENV NODE_ENV=production
EXPOSE 3004

CMD ["npm", "start"]


