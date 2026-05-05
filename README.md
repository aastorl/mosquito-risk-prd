# MosquitoRisk - Product Requirements Document

## Document Overview

**Product Name:** MosquitoRisk  
**Platform:** iOS  
**Status:** Portfolio / Prototype (active development)  
**Version:** 1.1.1  
**Date:** May 2026  
**Author:** Astor Ludueña  
**Primary Audience for this Document:** Business Analyst / Product / Interview Review  

## Executive Summary

MosquitoRisk is an iOS application designed to help users understand **when mosquito activity risk is higher** in Rosario, Argentina, using **local weather conditions** and (optionally) the user’s **current location**.

The product is intentionally lightweight and iOS-native: users open the app, immediately see a risk label (Low/Medium/High) plus the underlying weather factors (temperature, humidity, precipitation, wind), and can explore a **risk map** with predefined zones (parks and islands) rendered as interactive overlays.

From a product perspective, MosquitoRisk is built around “fast value”: a minimal number of steps between launch and insight, plus optional **local notifications** that trigger only when the risk becomes **high**, with throttling to avoid alert fatigue.

Important scope note: risk levels are a **heuristic estimate** based on weather signals; the app is not a medical device and does not claim epidemiological accuracy.

## Problem Statement

In mosquito-prone cities, users often lack a simple, daily signal to answer:

- “Is today a high-risk day for mosquitoes in my area?”
- “Should I take extra precautions (repellent, avoid stagnant water, adjust outdoor plans)?”

Most alternatives are either:

- Too generic (weather apps that don’t translate conditions into mosquito risk)
- Too complex (public health dashboards not designed for quick daily decisions)
- Not local/contextual (lack location-based guidance or a city-specific map view)

MosquitoRisk solves this by converting weather inputs into an immediately understandable risk level, and pairing it with a map view that’s relevant to the user’s city.

## Product Goal

Provide a fast, trustworthy, and explainable iOS-native experience that helps users **recognize high-mosquito-risk conditions** and take basic precautions, with minimal friction.

## Target Audience

### Persona 1: Local Resident

Residents of Rosario who want a quick daily signal about mosquito activity risk.

**Typical scenarios**
- Planning outdoor activities
- Deciding whether to use repellent
- Identifying days to reduce exposure at dawn/dusk

**Needs**
- Immediate risk visibility
- Simple explanation of factors
- Low-friction daily use

### Persona 2: Family Caregiver

Users making decisions for kids/elderly family members and wanting a more cautious signal.

**Typical scenarios**
- Weekend plans in parks
- Choosing protective clothing/repellent
- Minimizing exposure on high-risk days

**Needs**
- Clear “high risk” alerting
- Easy-to-interpret UI
- Reduced false alarms (throttling)

### Persona 3: Visitor / Traveler

Users visiting Rosario who don’t know local patterns and want contextual guidance.

**Typical scenarios**
- Short stays, unfamiliar climate
- Tourist activities near rivers/parks

**Needs**
- Map-centric discovery
- City default behavior without setup
- Quick onboarding/intro

## User Stories

### User Story 1

As a user, I want to see the current mosquito risk for my location, so that I can decide whether to take precautions.

**Acceptance Criteria**
- The app requests location permission (When In Use) and handles deny/restrict states gracefully.
- If location is available, the app fetches weather data for the user’s coordinates.
- The UI displays a risk label (Low/Medium/High) and the key weather factors.
- If location is unavailable, the app shows a clear explanation and a path to Settings.

### User Story 2

As a user, I want to explore a risk map for Rosario, so that I can understand risk differences across zones (parks/islands).

**Acceptance Criteria**
- The app presents a map view with predefined zones for Rosario.
- Zones are rendered with color-coded overlays based on risk level.
- Each zone is labeled (annotation/callout).
- The map supports panning/zooming and can re-center to the city default region.

### User Story 3

As a user, I want to receive a notification when mosquito risk becomes high, so that I can take precautions without actively checking the app.

**Acceptance Criteria**
- The app requests notification permission.
- Notifications are sent only when the risk level is High.
- Notifications are throttled to prevent frequent repeat alerts (e.g., minimum 30 minutes).
- Users can disable notifications from in-app Settings and the app stops scheduling/delivering alerts.

### User Story 4

As a user, I want the app to behave well when there is no connectivity, so that I’m not stuck in a broken loading state.

**Acceptance Criteria**
- The app detects no-network conditions and shows an actionable fallback screen.
- The user can retry.
- The UI remains responsive and does not loop indefinitely.

## Functional Requirements

### FR1. Weather Data Fetch

- The system must fetch weather data for a given latitude/longitude via an HTTP endpoint.
- The system must decode the response into a stable internal model (`WeatherData`).
- The system must handle HTTP errors, empty payloads, and decoding failures.

### FR2. Risk Scoring Engine

- The system must compute a risk level (Low/Medium/High) from weather inputs.
- The system must be explainable at a factor level (temperature/humidity/precipitation/wind).
- The risk computation must be deterministic for a given input payload.

### FR3. Location-based Experience

- The system must request “When In Use” location access and update risk as location changes.
- The system must handle denial/restriction states and provide a Settings redirect.

### FR4. Risk Map for Rosario

- The system must include a predefined set of Rosario zones (parks/islands) with coordinates.
- The system must fetch/compute a risk level per zone and render it as a map overlay.
- The system must display zone labels and visually distinguish categories where relevant.

### FR5. Notifications

- The system must request notification permission and expose an in-app toggle.
- The system must send notifications only for High risk, with throttling.
- The system must remove pending/delivered notifications when disabled.

### FR6. Onboarding / Education

- The system must include an intro/education step explaining what the risk represents.
- The system must include an info view explaining the risk logic and limitations.

## Non-Functional Requirements

- **NFR-01:** The main risk label should render within 1 second after receiving a valid weather response.
- **NFR-02:** The app must support iOS 17.6 and above.
- **NFR-03:** The app must not crash or hang on network errors or permission denial.
- **NFR-04:** Notifications must be rate-limited to reduce alert fatigue.
- **NFR-05:** User privacy must be respected: location is used only to compute risk and is not stored or shared by default.
- **NFR-06:** The map experience must remain responsive during overlay rendering and user interaction.

## Success Metrics (KPIs)

### Acquisition and Activation

- **Location Permission Opt-in Rate:** % of users who grant When-In-Use location.
- **First-Session Value Rate:** % of users who see a risk result (not just a loading/fallback state) in the first session.
- **Map Discovery Rate:** % of users who open the Risk Map at least once.

### Engagement

- **Sessions per Active User per Week:** weekly usage frequency among active users.
- **Map Interactions per Session:** average pans/zooms/zone taps (proxy for exploration).
- **Notification Enable Rate:** % of users who keep notifications enabled after first week.

### Product Quality

- **Weather Fetch Success Rate:** % of attempts that yield a valid `WeatherData`.
- **Fallback Rate:** % of sessions that enter a no-network or no-location state.
- **Alert Relevance Proxy:** % of high-risk notifications followed by an app open within 10 minutes.

## Technical Context

- **Language:** Swift  
- **UI Framework:** SwiftUI  
- **Architecture:** MVVM (simple)  
- **Location:** CoreLocation  
- **Map:** MapKit (overlays via `MKMapView` bridge)  
- **Networking:** URLSession + JSON decoding (via a proxy endpoint)  
- **Connectivity:** NWPathMonitor  
- **Notifications:** UserNotifications (local)  
- **Media:** AVKit for background video, Lottie for animations  

## Assumptions

- Weather data is available and reliable enough to serve as a proxy signal for mosquito activity.
- Users understand that the risk level is an estimate and not a medical claim.
- The majority of usage happens inside Rosario (city-scoped design is acceptable for the product’s intent).

## Risks

- **Data/Units Mismatch Risk:** upstream weather units (wind/precipitation) could be misinterpreted, skewing risk.
- **Trust Risk:** users may over-trust the score if disclaimers are not clear.
- **Reliability Risk:** dependency on a single proxy endpoint may cause outages or degraded UX.
- **Scope Risk:** Rosario-only positioning may limit broader adoption without a multi-city strategy.

## Out of Scope (Current Version)

- Epidemiological validation, public health case integration, or “official” risk scoring
- Cloud accounts, syncing, or user profiles
- Crowdsourced mosquito sightings/report submission
- Background fetch or server-driven push notifications
- Android/web versions
- Monetization

## Future Opportunities

- Multi-city support with a configurable zone dataset (JSON/plist/back-end)
- Caching with TTL per coordinate to reduce repeated fetches
- Seasonal calibration and clearer “why this risk” breakdown UI
- Historical trends (risk over the last N days) and simple forecasting
- Optional integrations with local advisories (with strong disclaimers)

## Glossary

- **Risk Level:** Low/Medium/High classification produced by the scoring engine.
- **WeatherData:** internal model containing temperature/humidity/precipitation/wind and day/night context.
- **Zone:** predefined geographic area (park/island) used in the Rosario risk map.
- **Overlay:** visual region drawn on MapKit (e.g., `MKCircle`) to represent zone risk.
- **Throttling:** rate limiting for notifications to avoid repeated alerts.
