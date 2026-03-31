# CAPI (Control API)

[Live Demo](https://CAPI-web-app.vercel.app)

**Developer:** Newton Script  
**Platform:** Industrial IoT, PLC APIs  
**Backend:** Rust  
**Database:** Postgres  
**Frontend:** Any client (GraphQL / REST / WebSocket)  
**License:** MIT / ISC  

---

## Project Overview
CAPI is an industrial IoT platform that transforms your PLCs (Programmable Logic Controllers) into modern APIs in minutes. It allows querying live data, historical trends, and subscribing to real-time updates without any middleware. Type-safe GraphQL-like APIs are auto-generated from your PLC configuration, with all readings stored in Postgres for analytics and reporting. Security is built-in, including JWT authentication, API key management, rate limiting, and logging.

## Features
- Connect any PLC and auto-discover tags/signals
- GraphQL, REST, and WebSocket APIs
- Historical analytics stored in Postgres
- Real-time dashboards for temperature, pressure, motor speed
- Secure by default: API keys, JWT, rate limiting, request logging
- Auto-generated API documentation
- Developer-first experience: integrate in minutes

## How It Works
1. **Connect PLC** – Add your PLC by IP. Supported protocols: Siemens, Allen-Bradley, Mitsubishi, Modbus, OPC-UA.  
2. **Configure Endpoints** – Select which tags to expose, map types, set polling intervals. Schema auto-generates.  
3. **Access Your API** – Get base URL and API key. Query live and historical data instantly.

## Example: Query Live PLC Data
```javascript
const response = await fetch("https://api.capi.io/v1/graphql", {
  method: "POST",
  headers: {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    query: `{
      plc(id: "assembly-line-a") {
        motorSpeed
        temperature
        conveyorActive
      }
    }`
  })
});
const { data } = await response.json();
console.log(data);
## Example: Query Live PLC Data
```javascript
const response = await fetch("https://api.capi.io/v1/graphql", {
  method: "POST",
  headers: {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    query: `{
      plc(id: "assembly-line-a") {
        motorSpeed
        temperature
        conveyorActive
      }
    }`
  })
});
const { data } = await response.json();
console.log(data);
