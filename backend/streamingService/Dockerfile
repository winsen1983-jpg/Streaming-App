FROM node:18-alpine AS base
WORKDIR /app/streamingService

# package.json கோப்புகளை நகலெடுத்தல் (இறுதியில் ஒரு புள்ளி இருப்பதை உறுதி செய்யவும்)
COPY package*.json ./
RUN npm install --production

# மற்ற அனைத்து கோப்புகளையும் நகலெடுத்தல் (இடையில் ஒரு இடைவெளி மற்றும் புள்ளி இருக்க வேண்டும்)
COPY . .

ENV NODE_ENV=production
EXPOSE 3002
CMD ["npm", "run", "start"]

