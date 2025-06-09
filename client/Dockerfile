FROM node:18-alpine

WORKDIR /app
COPY . .

RUN npm install

# If you want to build:
RUN npm run build

EXPOSE 3000

CMD ["npm", "start"]
                                 