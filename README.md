Arquitecture overview

1. Single Data Source
Both schedulers consume the same Mock Augur API (GET /indicators)

QRadar takes all indicator types (IP, domain, url, hash)

EDL takes only IPs with specific filters (confidence ≥ 70, threat_level = high/critical)

2. Shared Persistence (Object Store)
The EDL scheduler writes IPs and their metadata

Public endpoints read from Object Store to serve the blocklist and metadata

3. Simulated External APIs
Mock Augur: Provides the input threat data

Mock QRadar: Receives and validates the transformed payloads

4. APIs Exposed by the App
EDL endpoints: Consumed by the firewall (or for manual testing)

Metadata endpoint: For monitoring and statistics

---------------------

 Setup Instructions – How to Import and Run the Integration
Follow these steps to set up and run the integration in Anypoint Studio:

1. Configure Authentication (First Time Only)
After installing Anypoint Studio:

Go to the Window menu at the top

Select Anypoint Studio → Authentication

Click Add and enter your Anypoint Platform credentials:

User: threat-intel
Pass: Threat-intel2026

Still in the Window menu, go to Anypoint Studio → API Manager

Enter your API Manager Client ID and Client Secret

client id: bd24f8d7ab5244f3a319deb3eebbc0a3
client secret: D9C87e254b4E4Af693012715cCb78f6f

Click Apply and Close

2. Import the Project
In Package Explorer, right‑click anywhere

Select Import... from the menu

In the import wizard, expand Anypoint Studio

Choose Anypoint Studio Project from File System and click Next

Click Browse, navigate to the folder where you unzipped the take‑home project, and select it

Make sure the project appears in the list and is checked

Click Finish

3. Run the Application
In the toolbar, click the Run button’s dropdown arrow

Select Run Configurations...

In the left panel, double‑click Mule Application

On the right, under Name, make sure the project threat-intel is selected

Click Run

The application will start, and all scheduled jobs (QRadar sync, EDL update) will begin running automatically.

Done. The integration is now running locally.

---------------------

All endpoints named here can be imported to postman via the postman collection i left in readnme and utility folder

1. Test Palo Alto EDL Integration
A. Check the EDL List

GET /api/edl/malicious-ips
Headers: Authorization: Basic base64(augur:secret)

Expected output (example):


# Augur Security - Malicious IP Block List
# Last Updated: 2024-12-20T15:30:00Z
# Total IPs: 3
# Threat Levels: Critical: 2, High: 1
# IOC: ioc-001 | Threat: critical | Campaign: Operation CloudHopper | Last Seen: 2024-12-20
45.142.212.61
# IOC: ioc-005 | Threat: critical | Campaign: Mirai Variant Campaign | Last Seen: 2024-12-20
103.253.145.28
# IOC: ioc-002 | Threat: high | Campaign: Financial Sector Campaign | Last Seen: 2024-12-20
185.220.101.32
B. Check Metadata Endpoint

GET /api/edl/malicious-ips/metadata
Headers: Authorization: Basic base64(augur:secret)

Expected output:

json
{
  "last_updated": "2024-12-20T15:30:00Z",
  "total_ips": 3,
  "threat_levels": {
    "critical": 2,
    "high": 1
  },
  "oldest_indicator": "2024-12-18T10:15:00Z",
  "newest_indicator": "2024-12-20T14:22:00Z",
  "expired_count": 0
}
2. Test Mock APIs
Mock Augur API

GET /api/v1/indicators?type=ip&min_confidence=70&threat_level=high,critical
Returns only IPs matching filters.

Mock QRadar API

POST /api/reference_data/sets/bulk_load/ip
Headers:

SEC: 12345678-1234-1234-1234-123456789abc

Content-Type: application/json

Body example:


{
  "name": "AugurThreatIntel_IPs",
  "element_type": "IP",
  "timeout_type": "LAST_SEEN",
  "data": []
}

Returns 200 OK (or 409/422 depending on payload).

---------------------

One big assumption I made was not doing the project in the recommended technologies. Since the position was reported to be for MuleSoft, I wanted to showcase my skills in this platform rather than simply copying and pasting AI-generated code in Python.

This decision allowed me to demonstrate:

Hands‑on experience with Anypoint Studio

Real understanding of Mule components (HTTP connectors, Object Store, DataWeave transformations, schedulers)

Ability to design integration flows that match enterprise requirements

While Python might have been faster, building this in MuleSoft reflects the actual tech stack used in the role and proves I can deliver production‑ready integrations using the tools the team relies on.