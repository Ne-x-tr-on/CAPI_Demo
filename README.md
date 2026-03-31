# CAPI (Control API)

CAPI is an industrial IoT platform that turns your PLCs (Programmable Logic Controllers) into modern APIs in minutes. It supports Siemens, Allen-Bradley, Mitsubishi, Modbus, and OPC-UA PLCs, allowing you to query live data, historical trends, and subscribe to real-time updates without any middleware. Type-safe GraphQL-like APIs are auto-generated from your PLC configuration, with all readings stored in Postgres for analytics and historical reporting. Secure by default with JWT authentication, API keys, rate limiting, and logging.

## Features
- Connect any PLC and auto-discover tags/signals
- GraphQL, REST, and WebSocket APIs
- Historical analytics stored in Postgres
- Real-time dashboards for temperature, pressure, motor speed
- Secure by default: API keys, JWT, rate limiting, request logging
- Auto-generated API documentation

## How It Works
1. **Connect PLC** – Add your PLC by IP. Supported protocols: Siemens, AB, Mitsubishi, Modbus, OPC-UA.  
2. **Configure Endpoints** – Select tags to expose, map types, set polling intervals. Schema auto-generates.  
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
