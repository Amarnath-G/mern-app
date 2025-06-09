FROM node:18-alpine

WORKDIR /app
COPY . .

RUN npm install

# Optional: If you have build steps
#RUN npm run build

EXPOSE 5000
CMD ["node", "server.js"]

