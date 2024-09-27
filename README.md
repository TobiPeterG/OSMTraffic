# Traffic Data Backend for OpenStreetMap: Project Overview
## Project Summary
This project aims to build a traffic data aggregation server for Organic Maps *(OM)* that can collect, cache, and serve traffic information to enhance real-time routing and navigation for users. The solution leverages multiple traffic data sources, primarily from Datex2 and other national traffic APIs, and aims to be scalable, efficient, and open for use by Organic Maps and potentially other OpenStreetMap-based applications.

The system will periodically collect traffic data, cache it efficiently using Redis, and serve it to users via a RESTful API. This solution will be scalable and efficient to handle thousands of requests per second *(RPS)*, with the potential to expand to millions of users over time.

## Current Considerations
### Data Sources
- **Datex2:** The primary source for traffic information in the European Union and other countries like Norway, Iceland, and the UK. Datex2 provides traffic, parking, electromobility infrastructure, and traffic regulation data via a standardized XML-based API.
- **Other APIs:** Countries like New Zealand and the USA do not contribute to Datex2, and the traffic data must be sourced through scraping official websites or utilizing government-owned APIs.
Backend Technologies

### Tech Stack

- **Golang:** Chosen for its scalability, ease of use, and strong performance in handling high-load environments. Go is the preferred choice for the backend implementation due to its extensive ecosystem for network programming and efficient concurrency handling.
- **Redis:** A highly efficient in-memory caching system to store traffic data. Caching will reduce external API calls and ensure smooth user experience while avoiding quota issues.
- **Caddy:** To be used as a reverse proxy for handling HTTPS requests with Let's Encrypt and potentially load-balancing across multiple backend instances.

### Server Architecture

- **HTTP Routing:** Use `Chi` or standard `net/http` in Go for routing API requests.
- **Caching:** Implement caching of traffic data using Redis, with an efficient eviction policy to keep recent traffic updates available without constantly requesting external APIs.
- **Data Aggregation:** Combine traffic data from different sources (Datex2 and others) into a standard response format like GeoJSON for ease of integration on the client-side.

### Key Challenges
- **Mapping Real-World Data to OSM:** One major challenge is ensuring that traffic data received from various sources *(Datex2, APIs)* accurately maps to road segments in OpenStreetMap *(OSM)*. This includes handling mismatches in road names or coordinates.
- **Handling Quota Limits:** Datex2 and other APIs may have rate limits. The system must handle these limitations through caching and efficient data request strategies.
- **Data Precision:** Traffic data is collected every 5-10 minutes, so ensuring timely updates and reflecting changes in traffic conditions is key for accuracy.

## Key Functionalities
### Traffic Data Collection

The server will fetch traffic data every 5-10 minutes from Datex2 and other sources via API or scraping.
Cache the fetched data in Redis for a specified duration to reduce unnecessary API requests and adhere to quota limits.
### API Endpoints

- **/traffic:** The main API endpoint for fetching real-time traffic data. The server responds with GeoJSON or a custom format containing traffic incidents, road closures, and congestion information.
- **/report:** Users can report traffic jams or incidents manually to contribute to traffic data.

### Redis Caching

Caching will reduce external requests, store data in memory, and serve quick responses to clients. Redis will also allow us to run multiple instances of the backend with replication to ensure availability.

### Client-Server Interaction

A service/app will send a request for traffic data when generating a route. The server will return real-time traffic data relevant to the route in response. The service/app can then display this data and adjust routes based on current conditions.

## Expansion
The server can be adapted to support other OpenStreetMap-based apps like Organic Maps or OsmAnd, which can use the traffic data for better routing decisions.

## Planned Architecture
### Backend Components
- **Golang Server:** Handles HTTP requests and fetches traffic data.
- **Redis Cache:** Stores traffic data and reduces load on external APIs.
- **Caddy:** Manages HTTPS, load balancing, and reverse proxy setup for backend scalability.
### Data Flow
- Periodically fetch traffic data from Datex2 and other sources.
- Cache the results in Redis with a time-to-live *(TTL)* based on the update frequency of each data source.
- Serve cached traffic data to client requests.
- Monitor and handle high load through Redis, Golang concurrency, and load-balancing with Caddy.

## Project Phases
### Phase 1: Prototyping

- Set up a basic Golang HTTP server with Redis caching and a simple `/traffic` endpoint.
- Fetch and parse traffic data from Datex2 APIs.
- Build a proof of concept that serves traffic data to a basic service.

### Phase 2: Client Integration

- Modify the Organic Maps codebase to request traffic data from the new backend and display traffic information in the app.
- Validate the accuracy of traffic data mapping with OSM roads.

### Phase 3: Optimization and Scaling

- Implement Redis-based caching with proper TTL settings.
- Load testing to ensure the system can handle increasing QPS *(queries per second)* as the user base grows.
- Set up Caddy for load-balancing and SSL certificate management.

### Phase 4: Expansion

- Add support for more countries and data sources.
- Allow user-contributed traffic reports.
- Extend compatibility with other OpenStreetMap-based applications.

## Development and Collaboration
- **Repository:** The server code will be open-sourced to allow for community collaboration and improvements.
- **Language:** Golang is the primary language for backend development, with Redis for caching.
- **Documentation:** Comprehensive documentation for setup, configuration, and API endpoints will be provided.

### Current progress
- **Server prototype:** https://github.com/TobiPeterG/OSMTraffic-server

## Resources and References
- Datex2 Documentation: https://datex2.eu/
- Organic Maps GitHub: https://github.com/organicmaps/organicmaps
- Redis Documentation: https://redis.io/documentation
- Golang Documentation: https://golang.org/doc/
### Similar projects
- TrafficPixels (velocity based traffic): https://trafficpixel.tk/trafficSql/introductionTrafficSql.php
- Open Traffic Collection (Traffic information sources): https://github.com/graphhopper/open-traffic-collection

## Collaborators
Open to contributions from the community, especially those familiar with Datex2 and traffic data systems.
This README will be updated as the project progresses. Stay tuned for more updates and feel free to contribute or suggest improvements! :)
