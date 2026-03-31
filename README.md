# CAPI (Control API)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Rust](https://img.shields.io/badge/built%20with-Rust-orange)](https://www.rust-lang.org/)
[![Postgres](https://img.shields.io/badge/database-Postgres-blue)](https://www.postgresql.org/)
[![Live Demo](https://img.shields.io/badge/demo-live-green)](https://capi-web-api.vercel.app)

> **Industrial APIs in minutes, not months** — Turn your PLCs into modern APIs instantly

**Developer:** Newton Kamau

**Platform:** Industrial IoT, PLC APIs  

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [How It Works](#how-it-works)
- [Infrastructure Architecture](#infrastructure-architecture)
- [API Documentation](#api-documentation)
  - [Authentication](#authentication)
  - [List All PLCs](#list-all-plcs)
  - [Get PLC Tags (Live Values)](#get-plc-tags-live-values)
  - [Read Single Tag Value](#read-single-tag-value)
  - [Historical Queries](#historical-queries)
  - [Real-time Subscriptions](#real-time-subscriptions)
  - [Mutations](#mutations)
- [Code Examples](#code-examples)
  - [JavaScript/TypeScript](#javascripttypescript)
  - [Python](#python)
  - [cURL](#curl)
  - [WebSocket](#websocket)
- [Security](#security)
- [Getting Started](#getting-started)
- [Deployment](#deployment)
- [Performance Benchmarks](#performance-benchmarks)
- [Troubleshooting](#troubleshooting)
- [License](#license)

---

## Overview

CAPI is an industrial IoT platform that transforms your PLCs (Programmable Logic Controllers) into modern APIs in minutes. It allows querying live data, historical trends, and subscribing to real-time updates without any middleware. Type-safe GraphQL-like APIs are auto-generated from your PLC configuration, with all readings stored in Postgres for analytics and reporting.

### What Makes CAPI Different?

- **No middleware needed** — Direct connection from PLC to API
- **Auto-generated schemas** — Your API documentation updates automatically when you change PLC configs
- **Live + historical in one query** — Combine real-time PLC data with Postgres analytics
- **Developer-first** — Works with any language or framework, zero learning curve

---

## Features

### Core Capabilities

| Feature | Description |
|---------|-------------|
| **GraphQL-Like API** | Query exactly what you need. Type-safe schemas auto-generated from PLC config |
| **Postgres Storage** | All readings automatically persisted. Query historical trends, run aggregates, export analytics |
| **Real-Time WebSocket** | Subscribe to tag changes with instant push updates |
| **Secure by Default** | JWT authentication, API key management, rate limiting, request logging |

### Industrial Features

| Feature | Description |
|---------|-------------|
| **Connect Any PLC** | Siemens, Allen-Bradley, Mitsubishi, Modbus, OPC-UA support |
| **Auto-Discovery** | Automatically discover tags and signals from connected PLCs |
| **Live Dashboards** | Real-time charts for temperature, pressure, motor speed |
| **Historical Analytics** | Average, min, max, standard deviation from Postgres |
| **Auto-Generated Docs** | API documentation always up-to-date with your config |
| **Multiple Protocols** | Modbus TCP/RTU, OPC-UA, Siemens S7, Ethernet/IP |

---

## How It Works

### Three Steps from PLC to Production-Ready API

#### **01 — Connect PLC**
Add your PLC by IP address. We support Siemens, Allen-Bradley, Mitsubishi, Modbus, and OPC-UA.
- Auto-discover tags and signals
- Automatic protocol detection
- Connection status monitoring

#### **02 — Configure Endpoints**
Select which tags to expose, map types, set polling intervals. Your API schema auto-generates.
- Visual tag configuration UI
- Custom data type mapping
- Configurable polling intervals (100ms to 60s)
- Tag grouping and naming

#### **03 — Access Your API**
Get your base URL and API key. Query live data and historical trends instantly.
- GraphQL + REST + WebSocket endpoints
- Auto-generated documentation
- Ready-to-use client libraries

---

## Infrastructure Architecture
### System Architecture Overview

| Layer | Components | Technologies | Purpose |
|-------|------------|--------------|---------|
| **Factory Layer** | Siemens S7-1500 • Allen-Bradley CompactLogix • Mitsubishi FX • Modbus Devices • OPC-UA Servers | Profinet • EtherNet/IP • Modbus TCP/RTU • OPC-UA • S7 Protocol | Industrial controllers providing real-time process data |
| **Protocol Adapters** | Modbus TCP/RTU Driver • OPC-UA Client • Siemens S7 Driver • Allen-Bradley CIP • Mitsubishi MC Protocol | tokio-modbus • opcua-client • s7-comm • ethernet-ip • mitsubishi-mc | Protocol-specific communication bridges converting industrial protocols to internal data format |
| **Polling Engine** | Tag Scheduler • Priority Manager • Retry Handler • Connection Pool | Tokio async runtime • Channel-based messaging • Exponential backoff | Scheduled reading of PLC tags with configurable intervals, priorities, and automatic reconnection |
| **API Gateway** | GraphQL Endpoint • REST API • WebSocket Server • Rate Limiter • Auth Middleware | Actix-web • async-graphql • JWT • API Keys • RBAC | Unified entry point handling all client requests with authentication, rate limiting, and request routing |
| **Business Logic** | Tag Management • Data Aggregation • Alert Engine • Audit Logger • Schema Generator | Rust business layer • Rule engine • TimescaleDB continuous aggregates | Core application logic including tag discovery, data transformation, alert evaluation, and audit trail |
| **Cache & Queue** | Live Value Cache • Write Buffer • Session Store • Rate Limit Counter | Redis • In-memory hashmap • Channel buffers • Connection pooling | High-performance caching for hot data, buffering writes, and managing active sessions |
| **Storage Layer** | Time-Series Data • Aggregates • Configuration • Auth Data • Alert History | PostgreSQL • TimescaleDB extension • JSONB • Full-text search | Persistent storage with time-series optimization for historical data, rollups, and system configuration |
| **Client Layer** | Web Dashboard • Mobile App • Data Science • Alerting • MQTT Bridge • Grafana | React • React Native • Python • Webhooks • Slack/Teams • Prometheus | Consumer applications accessing data via APIs for visualization, analytics, automation, and integration |

### Data Flow

| Step | Source | Destination | Data | Description |
|------|--------|-------------|------|-------------|
| 1 | PLC | Polling Engine | Raw tag values | Polling engine reads tags at configured intervals (100ms-60s) |
| 2 | Polling Engine | In-Memory Cache | Current values | Latest values cached for instant API responses |
| 3 | Polling Engine | Write Queue | Value + timestamp | Values queued for asynchronous database writes |
| 4 | Write Queue | TimescaleDB | Historical readings | Batched writes to time-series tables with automatic partitioning |
| 5 | TimescaleDB | Aggregator | Raw data | Background worker computes hourly/daily aggregates |
| 6 | Aggregator | Aggregate Tables | Rollup data | Pre-computed statistics for fast historical queries |
| 7 | WebSocket Server | Subscribers | Real-time pushes | Live data pushed to connected clients via GraphQL subscriptions |

### Component Specifications

| Component | Implementation | Scaling Strategy | Failure Mode |
|-----------|---------------|------------------|--------------|
| API Gateway | Actix-web with async-graphql | Horizontal with load balancer | Circuit breaker degrades to read-only |
| Protocol Adapters | Tokio tasks per PLC | Per-PLC task isolation | Exponential backoff, dead-letter queue |
| Polling Engine | Async work queue | Dynamic task pool | Graceful degradation, priority queuing |
| Redis Cache | Redis Cluster | Sharded by PLC ID | Cache miss falls back to Postgres |
| Postgres | Primary + Replicas | Read replicas for queries | Write-ahead log for point-in-time recovery |
| WebSocket Server | Connection per client | Sticky sessions | Reconnection with state recovery |

### Network Topology

| Zone | Components | Ports | Security |
|------|------------|-------|----------|
| **Factory DMZ** | PLCs • Protocol Adapters | 502 (Modbus) • 4840 (OPC-UA) • 102 (S7) • 44818 (EIP) | VLAN isolation • Allowlisted IPs • Read-only service accounts |
| **Application Tier** | API Gateway • Polling Engine • Business Logic | 8080 (HTTP) • 8081 (Metrics) • 6379 (Redis) | Internal network • mTLS between services • JWT validation |
| **Data Tier** | PostgreSQL • Redis | 5432 (Postgres) • 6379 (Redis) | Private subnet • Encrypted at rest • Backup replication |
| **Client Access** | CDN • Load Balancer | 443 (HTTPS) • 80 (Redirect) | WAF • DDoS protection • API key validation |

### Architecture Diagram

---

## API Documentation

Auto-generated from your PLC endpoint configuration. Includes live & historical Postgres queries.

### Authentication

All API requests require authentication. Use your API key in the Authorization header:

```http
Authorization: Bearer lv_prod_your_api_key_here
To get your API key:

Log in to your CAPI dashboard

Navigate to Settings → API Keys

Generate a new key with appropriate permissions

List All PLCs
Returns all PLCs associated with your account with live status from PLC connection.

Endpoint: POST https://api.capi.io/v1/graphql

Query:

graphql
query {
  plcs {
    id
    name
    model
    ipAddress
    status
    lastUpdated
    tagsCount
  }
}
Response:

json
{
  "data": {
    "plcs": [
      {
        "id": "plc-1",
        "name": "Assembly Line A",
        "model": "Siemens S7-1500",
        "ipAddress": "192.168.1.10",
        "status": "online",
        "lastUpdated": "2026-03-07T10:30:00Z",
        "tagsCount": 24
      },
      {
        "id": "plc-2",
        "name": "Packaging Line B",
        "model": "Allen-Bradley CompactLogix",
        "ipAddress": "192.168.1.20",
        "status": "online",
        "lastUpdated": "2026-03-07T10:30:00Z",
        "tagsCount": 42
      }
    ]
  }
}
Get PLC Tags (Live Values)
Retrieve all exposed tags with current live values from the PLC.

Query:

graphql
query {
  tags(plcId: "plc-1") {
    name
    type
    direction
    value
    pollingInterval
    unit
    description
    quality
  }
}
Response:

json
{
  "data": {
    "tags": [
      {
        "name": "motor_speed",
        "type": "float",
        "direction": "output",
        "value": 1450.5,
        "pollingInterval": 1000,
        "unit": "RPM",
        "description": "Main conveyor motor speed",
        "quality": "good"
      },
      {
        "name": "temperature_sensor",
        "type": "float",
        "direction": "input",
        "value": 72.3,
        "pollingInterval": 2000,
        "unit": "°C",
        "description": "Temperature in zone 3",
        "quality": "good"
      },
      {
        "name": "conveyor_active",
        "type": "boolean",
        "direction": "output",
        "value": true,
        "pollingInterval": 500,
        "unit": null,
        "description": "Conveyor belt status",
        "quality": "good"
      },
      {
        "name": "pressure_gauge",
        "type": "float",
        "direction": "input",
        "value": 45.2,
        "pollingInterval": 1000,
        "unit": "PSI",
        "description": "Hydraulic pressure",
        "quality": "good"
      }
    ]
  }
}
Read Single Tag Value
Get the current value of a specific tag from the PLC.

Query:

graphql
query {
  tagValue(plcId: "plc-1", tagName: "motor_speed") {
    name
    value
    timestamp
    quality
    unit
  }
}
Response:

json
{
  "data": {
    "tagValue": {
      "name": "motor_speed",
      "value": 1450.5,
      "timestamp": "2026-03-07T10:30:00Z",
      "quality": "good",
      "unit": "RPM"
    }
  }
}
Historical Queries
Query historical data from Postgres with time-series analytics.

Time Series Data
Query:

graphql
query {
  tagHistory(
    plcId: "plc-1"
    tagName: "temperature_sensor"
    from: "2026-03-06T00:00:00Z"
    to: "2026-03-07T00:00:00Z"
    interval: "5m"
  ) {
    timestamp
    value
    quality
  }
}
Response:

json
{
  "data": {
    "tagHistory": [
      {
        "timestamp": "2026-03-06T00:00:00Z",
        "value": 71.2,
        "quality": "good"
      },
      {
        "timestamp": "2026-03-06T00:05:00Z",
        "value": 71.5,
        "quality": "good"
      },
      {
        "timestamp": "2026-03-06T00:10:00Z",
        "value": 71.8,
        "quality": "good"
      }
    ]
  }
}
Statistical Aggregates
Query:

graphql
query {
  tagStats(
    plcId: "plc-1"
    tagName: "motor_speed"
    hours: 24
  ) {
    avg
    min
    max
    stdDev
    count
    firstValue
    lastValue
  }
}
Response:

json
{
  "data": {
    "tagStats": {
      "avg": 1442.3,
      "min": 1380.0,
      "max": 1490.5,
      "stdDev": 28.7,
      "count": 86400,
      "firstValue": 1450.5,
      "lastValue": 1445.2
    }
  }
}
Multiple Tags Query
Query:

graphql
query {
  multipleTagsHistory(
    plcId: "plc-1"
    tagNames: ["motor_speed", "temperature_sensor", "pressure_gauge"]
    hours: 24
  ) {
    tagName
    history {
      timestamp
      value
    }
    stats {
      avg
      min
      max
    }
  }
}
Response:

json
{
  "data": {
    "multipleTagsHistory": [
      {
        "tagName": "motor_speed",
        "history": [
          {"timestamp": "2026-03-07T10:00:00Z", "value": 1450.5},
          {"timestamp": "2026-03-07T11:00:00Z", "value": 1460.2}
        ],
        "stats": {
          "avg": 1442.3,
          "min": 1380.0,
          "max": 1490.5
        }
      },
      {
        "tagName": "temperature_sensor",
        "history": [
          {"timestamp": "2026-03-07T10:00:00Z", "value": 72.3},
          {"timestamp": "2026-03-07T11:00:00Z", "value": 73.1}
        ],
        "stats": {
          "avg": 71.8,
          "min": 70.2,
          "max": 74.5
        }
      }
    ]
  }
}
Real-time Subscriptions
Subscribe to live tag updates via WebSocket.

WebSocket URL: wss://api.capi.io/v1/graphql

Subscription:

graphql
subscription {
  tagUpdates(plcId: "plc-1", tags: ["motor_speed", "temperature_sensor"]) {
    name
    value
    timestamp
    quality
  }
}
Response Stream:

json
{
  "data": {
    "tagUpdates": {
      "name": "motor_speed",
      "value": 1450.5,
      "timestamp": "2026-03-07T10:30:00.123Z",
      "quality": "good"
    }
  }
}
Subscription with Filtering:

graphql
subscription {
  tagUpdates(
    plcId: "plc-1", 
    tags: ["motor_speed"], 
    minChange: 10.0
  ) {
    name
    value
    timestamp
    previousValue
    delta
  }
}
Mutations
Write data back to PLCs or configure system settings.

Write Tag Value
Query:

graphql
mutation {
  writeTag(
    plcId: "plc-1"
    tagName: "setpoint_temperature"
    value: 75.0
  ) {
    success
    message
    timestamp
  }
}
Response:

json
{
  "data": {
    "writeTag": {
      "success": true,
      "message": "Value written successfully",
      "timestamp": "2026-03-07T10:30:00Z"
    }
  }
}
Write Multiple Tags
Query:

graphql
mutation {
  writeMultipleTags(
    plcId: "plc-1"
    writes: [
      { tagName: "setpoint_temperature", value: 75.0 }
      { tagName: "setpoint_pressure", value: 50.0 }
      { tagName: "conveyor_speed", value: 1200.0 }
    ]
  ) {
    successCount
    failedCount
    results {
      tagName
      success
      message
    }
  }
}
Response:

json
{
  "data": {
    "writeMultipleTags": {
      "successCount": 3,
      "failedCount": 0,
      "results": [
        {"tagName": "setpoint_temperature", "success": true, "message": "OK"},
        {"tagName": "setpoint_pressure", "success": true, "message": "OK"},
        {"tagName": "conveyor_speed", "success": true, "message": "OK"}
      ]
    }
  }
}
Code Examples
JavaScript/TypeScript
Live Data Query
javascript
const response = await fetch("https://api.capi.io/v1/graphql", {
  method: "POST",
  headers: {
    "Authorization": "Bearer lv_prod_your_api_key",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    query: `{
      plc(id: "assembly-line-a") {
        motorSpeed
        temperature
        conveyorActive
        pressure
      }
    }`
  })
});

const { data } = await response.json();
console.log(`Motor Speed: ${data.plc.motorSpeed} RPM`);
console.log(`Temperature: ${data.plc.temperature}°C`);
console.log(`Conveyor Active: ${data.plc.conveyorActive}`);
console.log(`Pressure: ${data.plc.pressure} PSI`);
Historical Analytics
javascript
const response = await fetch("https://api.capi.io/v1/graphql", {
  method: "POST",
  headers: {
    "Authorization": "Bearer lv_prod_your_api_key",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    query: `{
      tagHistory(
        plcId: "plc-1"
        tagName: "temperature_sensor"
        hours: 24
      ) {
        timestamp
        value
      }
      tagStats(
        plcId: "plc-1"
        tagName: "temperature_sensor"
        hours: 24
      ) {
        avg
        min
        max
        stdDev
      }
    }`
  })
});

const { data } = await response.json();
console.log("24h Stats:", data.tagStats);
console.log("Time Series Data Points:", data.tagHistory.length);
Real-time Subscription
javascript
const ws = new WebSocket("wss://api.capi.io/v1/graphql");

// Authentication
ws.onopen = () => {
  ws.send(JSON.stringify({
    type: "connection_init",
    payload: { 
      headers: { 
        Authorization: "Bearer lv_prod_your_api_key" 
      } 
    }
  }));
  
  // Subscribe to updates
  ws.send(JSON.stringify({
    id: "1",
    type: "subscribe",
    payload: {
      query: `
        subscription {
          tagUpdates(plcId: "plc-1") {
            name
            value
            timestamp
          }
        }
      `
    }
  }));
};

ws.onmessage = (event) => {
  const message = JSON.parse(event.data);
  if (message.type === "data") {
    const update = message.payload.data.tagUpdates;
    console.log(`[${update.timestamp}] ${update.name}: ${update.value}`);
    
    // Trigger alerts based on values
    if (update.name === "temperature_sensor" && update.value > 80) {
      console.warn("⚠️ High temperature alert!");
    }
  }
};

ws.onerror = (error) => {
  console.error("WebSocket error:", error);
};

ws.onclose = () => {
  console.log("WebSocket disconnected");
};
Complete Client Example
javascript
class CapiClient {
  constructor(apiKey, baseUrl = "https://api.capi.io/v1") {
    this.apiKey = apiKey;
    this.baseUrl = baseUrl;
  }

  async query(query, variables = {}) {
    const response = await fetch(`${this.baseUrl}/graphql`, {
      method: "POST",
      headers: {
        "Authorization": `Bearer ${this.apiKey}`,
        "Content-Type": "application/json"
      },
      body: JSON.stringify({ query, variables })
    });
    return response.json();
  }

  async getPlcs() {
    return this.query(`{ plcs { id name status } }`);
  }

  async getTagValue(plcId, tagName) {
    return this.query(`
      query($plcId: ID!, $tagName: String!) {
        tagValue(plcId: $plcId, tagName: $tagName) {
          name value timestamp
        }
      }
    `, { plcId, tagName });
  }

  async getHistoricalStats(plcId, tagName, hours = 24) {
    return this.query(`
      query($plcId: ID!, $tagName: String!, $hours: Int!) {
        tagStats(plcId: $plcId, tagName: $tagName, hours: $hours) {
          avg min max stdDev
        }
      }
    `, { plcId, tagName, hours });
  }

  subscribe(plcId, onUpdate) {
    const ws = new WebSocket(`${this.baseUrl.replace("http", "ws")}/graphql`);
    
    ws.onopen = () => {
      ws.send(JSON.stringify({
        type: "connection_init",
        payload: { headers: { Authorization: `Bearer ${this.apiKey}` } }
      }));
      
      ws.send(JSON.stringify({
        id: "1",
        type: "subscribe",
        payload: {
          query: `
            subscription($plcId: ID!) {
              tagUpdates(plcId: $plcId) {
                name value timestamp
              }
            }
          `,
          variables: { plcId }
        }
      }));
    };
    
    ws.onmessage = (event) => {
      const message = JSON.parse(event.data);
      if (message.type === "data") {
        onUpdate(message.payload.data.tagUpdates);
      }
    };
    
    return ws;
  }
}

// Usage
const client = new CapiClient("lv_prod_your_api_key");
const plcs = await client.getPlcs();
console.log("Connected PLCs:", plcs);

const motorSpeed = await client.getTagValue("plc-1", "motor_speed");
console.log("Motor speed:", motorSpeed);

const stats = await client.getHistoricalStats("plc-1", "temperature_sensor", 24);
console.log("24h stats:", stats);
Python
python
import requests
import json
import asyncio
import websockets
from typing import Dict, Any

class CapiClient:
    def __init__(self, api_key: str, base_url: str = "https://api.capi.io/v1"):
        self.api_key = api_key
        self.base_url = base_url
        self.headers = {
            "Authorization": f"Bearer {api_key}",
            "Content-Type": "application/json"
        }
    
    def query(self, query: str, variables: Dict = None) -> Dict:
        """Execute a GraphQL query"""
        payload = {"query": query}
        if variables:
            payload["variables"] = variables
        
        response = requests.post(
            f"{self.base_url}/graphql",
            headers=self.headers,
            json=payload
        )
        response.raise_for_status()
        return response.json()
    
    def get_plcs(self) -> Dict:
        """Get all PLCs"""
        query = """
        query {
            plcs {
                id
                name
                model
                status
                tagsCount
            }
        }
        """
        return self.query(query)
    
    def get_tag_value(self, plc_id: str, tag_name: str) -> Dict:
        """Get current value of a tag"""
        query = """
        query GetTagValue($plcId: ID!, $tagName: String!) {
            tagValue(plcId: $plcId, tagName: $tagName) {
                name
                value
                timestamp
                quality
            }
        }
        """
        return self.query(query, {"plcId": plc_id, "tagName": tag_name})
    
    def get_historical_stats(self, plc_id: str, tag_name: str, hours: int = 24) -> Dict:
        """Get historical statistics"""
        query = """
        query GetStats($plcId: ID!, $tagName: String!, $hours: Int

