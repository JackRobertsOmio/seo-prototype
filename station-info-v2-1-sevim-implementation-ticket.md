# Station Info V2.1: Structure & Extraction MVP

Owner: Sevim  
Prototype: `station-info-v2-1-prototype.html`  
Public prototype: https://jackrobertsomio.github.io/seo-prototype/station-info-v2-1-prototype.html  
Example route: Barcelona to Madrid trains  
Scope: Route-page station information component

## Summary

Implement Station Info V2.1 as a no-new-data MVP that reuses existing station/place payload data in a clearer, route-specific, SEO-friendly component.

The goal is not to add new station data. The goal is to make existing data easier for users, search engines and LLMs to extract:

- Which station pair is most common for the route.
- Which station(s) a route departs from.
- Which station(s) a route arrives at.
- How to get to or from each station and the city centre.
- What facilities and services are available at each station.

The working prototype uses Barcelona to Madrid trains:

- Barcelona Sants
- Madrid-Puerta de Atocha-Almudena Grandes / Madrid Atocha
- Madrid Chamartín

## Context

Current station content exists in StationBoxes payloads, but it is often rendered as thin copy, icons, or raw amenity labels. This makes the content less useful for:

- SEO queries such as `Barcelona to Madrid train stations`, `Barcelona Sants to Madrid Atocha`, `Madrid Atocha facilities`, `getting from Madrid Atocha to city centre`.
- LLM extraction of station facts.
- Users trying to understand where they leave from, where they arrive, and what services exist at the station.

Competitor review showed:

- Trainline uses clear route-specific station copy and station-pair pages.
- Busbud and FlixBus expose stop/station options well for buses.
- Ferryhopper and Direct Ferries are strong on ferry route logistics, but mix port and operator-rule content.
- Omio has stronger structured station/place data than it currently renders.

## Product Principle

Use existing station data in a clearer, route-specific, answer-shaped format before adding new data.

## Core Scope

In scope:

- Implement the V2.1 train station component.
- Use existing station/place payload fields only.
- Add SEO intro with station-to-station answer.
- Link the station-pair page where available.
- Group stations by route side: departure and arrival.
- Render each station card with two panels:
  - City-centre transport
  - Station overview
- Convert public transport payload into mode-based rows.
- Convert amenities/facilities into grouped service summaries.
- Use natural station aliases in user-facing copy where useful.
- Keep canonical names, station IDs and coordinates in data/schema.
- Suppress missing/null/false fields.
- Preserve coordinates for schema/data attributes, but do not show raw coordinates to users.
- Add FAQ schema only for visible FAQ-style questions.

Out of scope:

- New station enrichment.
- Platform guidance.
- Entrance guidance.
- Taxi prices.
- Public transport fares.
- Walking times.
- Luggage prices.
- Lounge detail.
- Accessibility booking rules.
- Operator/onboard services for buses/ferries/flights.
- Ferry terminal/operator-terminal enrichment.
- Any unsupported “best way” recommendation.

## Component Structure

### 1. Header

Title pattern:

```text
[Origin] to [Destination] train stations
```

Example:

```text
Barcelona to Madrid train stations
```

SEO intro pattern:

```text
Most [origin] to [destination] trains use the [origin_station] to [primary_destination_station] station pair, with some services arriving at [secondary_destination_station]. Use this guide to compare [origin] and [destination] train stations on this route, check city-centre transport, and see station facilities including luggage storage, Wi-Fi, toilets, accessibility, parking, taxis, bike services and car rental.
```

Example copy:

```text
Most Barcelona to Madrid trains use the Barcelona Sants to Madrid Atocha station pair, with some services arriving at Madrid Chamartín. Use this guide to compare Barcelona and Madrid train stations on this route, check city-centre transport, and see station facilities including luggage storage, Wi-Fi, toilets, accessibility, parking, taxis, bike services and car rental.
```

Linking rule:

- If a station-to-station page exists, link the primary station-pair text.
- Example link text: `Barcelona Sants to Madrid Atocha`
- Example URL: `https://www.omio.es/trenes/barcelona-sants/madrid-atocha`

Primary/secondary rule:

- Use route-station booking share where available.
- Do not infer primary/secondary from total station booking volume.
- If route-station booking share is unavailable, use neutral ordering and avoid “most common” language.

### 2. Departure Group

Group heading:

```text
Departure station in [Origin]
```

FAQ question:

```text
Which [origin] station do trains to [destination] leave from?
```

FAQ answer pattern:

```text
Trains from [origin] to [destination] leave from [station_name]. The station is [distance] km from [origin] city centre at [address]. You can reach [station_name] by [available transport connections].
```

Example:

```text
Trains from Barcelona to Madrid leave from Barcelona Sants. The station is 1 km from Barcelona city centre at Plaça dels Països Catalans, 1, 7, 08014 Barcelona, Spain. You can reach Barcelona Sants by metro lines L3 and L5, bus lines A1 and A2, and regional and long-distance train services.
```

### 3. Arrival Group

Group heading:

```text
Arrival stations in [Destination]
```

FAQ question:

```text
Which [destination] station do trains from [origin] arrive at?
```

FAQ answer pattern:

```text
Trains from [origin] arrive at [primary_arrival_station]. [Secondary_arrival_station] is also shown as a [destination] station for this route. [Primary_arrival_station] is [distance] km from [destination] city centre, while [secondary_arrival_station] is [distance] km from [destination] city centre. Check your ticket before travel so you know which [destination] station your train uses.
```

Example:

```text
Trains from Barcelona arrive at Madrid-Puerta de Atocha-Almudena Grandes. Madrid Chamartín is also shown as a Madrid station for this route. Madrid-Puerta de Atocha-Almudena Grandes is 1 km from Madrid city centre, while Madrid Chamartín is 6 km from Madrid city centre. Check your ticket before travel so you know which Madrid station your train uses.
```

## Station Card Requirements

Each station card should show:

- Station name.
- Departure/arrival badge.
- Address where available.
- Distance to city centre where available.
- Phone where available.
- Expand/collapse body.
- Link to station page where available.

Station cards use two content panels.

## Panel 1: City-Centre Transport

Purpose: answer the station-to-city-centre atom.

Heading patterns:

```text
Getting to [departure_station] from [origin] city centre
Getting from [arrival_station] to [destination] city centre
```

Example heading:

```text
Getting to Barcelona Sants from Barcelona city centre
```

Answer sentence pattern:

```text
[Station] is [distance] km from [city] city centre and is connected by [available modes].
```

Mode rows:

```text
Metro: [metro lines]
Bus: [bus lines]
Train: [train lines]
```

Example:

```text
Barcelona Sants is 1 km from Barcelona city centre and is connected by metro, bus and train.

Metro: L3 and L5 serve Barcelona Sants.
Bus: A1 and A2 serve the station.
Train: AVE, Alvia, Euromed, Talgo, Trenhotel, TGV, Avant, Intercity, R1, R2, R2 Nord, R2 Sud, R3, R4, RG1, R11, R12, R13, R14, R15, R16 and R17 also serve Barcelona Sants.
```

Fallback:

```text
[Station] is [distance] km from [city] city centre.
```

Transport guardrails:

- Show only modes present in the payload.
- Do not invent travel times.
- Do not invent prices.
- Do not invent taxi costs.
- Do not invent walking routes.
- Do not invent line colours.
- Do not invent airport links.
- Do not make “best way” recommendations unless explicitly available in data.

## Panel 2: Station Overview

Purpose: summarize station facilities and services in practical traveler language.

Heading pattern:

```text
[Station alias] station facilities and services
```

Example:

```text
Madrid Atocha station facilities and services
```

Summary pattern:

```text
[Station] has the main services travelers need [before departure / after arriving], including [available services].
```

Example:

```text
Barcelona Sants has the main services travelers need before taking a Barcelona to Madrid train, including food and drink, Wi-Fi, luggage storage, ticket support, accessibility facilities, toilets, taxis, parking, bike services and car rental.
```

Recommended groups:

- Food and drink
- Wi-Fi and ATMs
- Tickets and information
- Luggage and lost property
- Parking, taxis and toilets
- Bike and car services
- Accessibility
- Shops, hotels and contact

Rendering rules:

- Render only groups with available data.
- Use detailed text where available.
- Use concise availability language where only boolean data exists.
- Suppress null/false fields.
- Do not show raw amenity chips as the primary content.

Examples:

```text
Wi-Fi hotspots are available in and near restaurants. ATMs are in the central part of the station.
```

```text
Parking, taxis and toilets are available at Barcelona Sants.
```

## Alias Rules

Use natural aliases in visible copy where they improve readability and search alignment:

- `Madrid Atocha` in headings and intro.
- `Barcelona Sants` in headings and intro.

Keep canonical names in:

- station card title when needed
- schema
- IDs
- data attributes
- station links

Example:

- Visible heading: `Madrid Atocha station facilities and services`
- Canonical card/entity: `Madrid-Puerta de Atocha-Almudena Grandes`

AI alias rule:

```text
Use the shortest commonly understood station alias in user-facing headings when it is unambiguous for the route. Preserve the canonical station name in structured data and station identifiers. Do not invent aliases; use only approved alias mappings or names present in Omio data.
```

## Structured Data

Add or preserve FAQPage schema for visible FAQ intros only:

- `Which Barcelona station do trains to Madrid leave from?`
- `Which Madrid station do trains from Barcelona arrive at?`

Do not add FAQ schema for intro copy unless it is rendered as a visible Q&A.

Add/keep TrainStation entities where safe:

- name
- address
- telephone where available
- geo coordinates where available
- station page URL as `@id`

Use `about` relationships from FAQ questions to relevant TrainStation entities.

## Route-Station Ranking Logic

Use route-station booking share for:

- primary station pair in the intro
- primary/secondary arrival station ordering
- primary/secondary labels where supported

Do not use total station booking volume for route rendering decisions.

Use total station booking volume only for:

- data audit priority
- deciding which station records to enrich first in V2.2

Fallbacks:

- If route-station share is unavailable, preserve payload order.
- Avoid “most common,” “primary,” or “main” unless supported.
- Use neutral wording such as `also shown as a station for this route`.

## Required Data Inputs

Route-level:

- origin city
- destination city
- travel mode
- route-station pair/share where available
- station-to-station page URL where available

Station-level:

- station ID
- station canonical name
- station alias where approved
- station page URL
- route side: departure / arrival
- address
- phone
- distance to city centre
- coordinates
- public transport connection strings
- ticket office hours
- Wi-Fi
- luggage storage
- parking
- dining
- ATM
- taxi
- toilets/WC
- accessibility
- information desk
- lost and found
- bike parking
- bike rental
- car rental
- hotels
- businesses/shops

## Example Barcelona to Madrid Payload Usage

Barcelona Sants:

- Use: name, address, phone, station link, ticket hours, transport, distance, Wi-Fi, luggage storage, parking, dining, ATM, taxi, toilets, accessibility, hotels, lost and found, ticket office, business, coordinates, bike parking, bike rental, car rental, information desk.

Madrid Atocha:

- Use: canonical name, alias, address, phone, station link, ticket hours, transport, distance, Wi-Fi, luggage storage, parking, dining, ATM, taxi, toilets, accessibility, hotels, lost and found, ticket office, businesses, coordinates, bike parking, bike rental, car rental, information desk.

Madrid Chamartín:

- Use: name, address, station link, distance, parking, dining, ATM, taxi, toilets, accessibility, hotels, lost and found, ticket office, coordinates, bike parking, bike rental, car rental, information desk.
- Suppress: null phone, null transport detail, null ticket hours, null Wi-Fi text, null luggage storage text.

## Cross-Mode Future Pattern

This ticket implements train station V2.1, but the component should be built so the rendering pattern can become `Travel Place Info V2.1`.

Mode variants:

- Train: `train stations`, `city-centre transport`, `station facilities and services`.
- Bus: `bus stations and stops`, `city-centre transport`, `bus station facilities and services`.
- Ferry: `ferry ports`, `port access`, `port facilities and services`.
- Flight: `airports`, `airport access`, `airport facilities and services`.

Bus note:

- Do not add onboard bus services to this component. That already exists elsewhere and is out of scope.

Ferry note:

- V2.1 should focus on port access and port facilities.
- V2.2 should add ferry/operator-rule data such as operator terminal mapping, check-in timing, vehicle boarding, foot passenger process, luggage rules, cabins, pets, bicycle-on-ferry rules and documents.

Flight note:

- V2.1 should focus on airport access and airport facilities.
- Airline rules should stay separate.

## Acceptance Criteria

- Component renders using existing payload data only.
- Header includes station-pair intro and link where available.
- Departure and arrival stations are grouped separately.
- FAQ intros render before the station cards for each route side.
- Each station card has `City-centre transport` and `Station overview` panels.
- Transport panel uses mode-based rows where mode data exists.
- Transport fallback uses distance only when mode data is missing.
- Station overview summarizes facilities and shows grouped detail.
- Null/false fields do not render empty UI.
- Coordinates are not visible to users.
- Station links are preserved.
- FAQ schema only includes visible FAQ questions.
- TrainStation schema validates syntactically.
- Mobile layout has no horizontal overflow.
- Accordions are keyboard accessible.
- No V2.2/high-decay facts appear unless already in the payload.

## QA Checklist

- Test a route with one departure and one arrival station.
- Test a route with one departure and multiple arrival stations.
- Test a station with rich transport details.
- Test a station with only distance and no transport details.
- Test a station with rich facility text.
- Test a station with boolean-only amenities.
- Test station page link presence and absence.
- Test station-to-station page link presence and absence.
- Validate FAQ schema.
- Validate TrainStation schema.
- Check mobile and desktop layout.

## Prototype

Public prototype:

```text
https://jackrobertsomio.github.io/seo-prototype/station-info-v2-1-prototype.html
```

Local prototype file:

```text
station-info-v2-1-prototype.html
```

Current local URL when server is running:

```text
http://127.0.0.1:8765/station-info-v2-1-prototype.html
```
