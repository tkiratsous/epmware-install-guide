# Target Applications

This section covers the configuration of EPMware connections to various Oracle EPM target applications including Planning Classic, Hyperion Financial Management (HFM), and Cloud applications (PCMCS/EPBCS).

## Overview

EPMware integrates with multiple Oracle EPM applications to deploy metadata and manage master data across your EPM landscape. Each target application requires specific configuration steps to enable seamless integration.

## Supported Target Applications

### On-Premise Applications

| Application | Version | Integration Method | Agent Required |
|------------|---------|-------------------|----------------|
| Oracle Hyperion Planning (Classic) | 11.1.2.4.x, 11.2.x | Direct API / LCM | Yes |
| Oracle Hyperion Financial Management (HFM) | 11.1.2.4.x, 11.2.x | HFM API | Yes |
| Oracle Essbase | 11.1.2.4.x, 19c, 21c | MaxL Scripts | Yes |
| Oracle EPMA | 11.1.2.4.x | EPMA API | Yes |

### Cloud Applications

| Application | Version | Integration Method | Agent Required |
|------------|---------|-------------------|----------------|
| Planning Cloud (PBCS/EPBCS) | Current | EPMAutomate | Yes |
| Profitability and Cost Management Cloud (PCMCS) | Current | EPMAutomate | Yes |
| Financial Consolidation Cloud (FCCS) | Current | EPMAutomate | Yes |
| Tax Reporting Cloud (TRCS) | Current | REST API | Optional |

## Application Registration

### Add Target Application

To register a new target application in EPMware:

1. Navigate to **Configuration → Applications**
2. Click **Add Application**
3. Enter application details
4. Configure connection parameters
5. Test connection
6. Save configuration

## Next Steps

Configure specific target applications:
- [Planning Classic](planning.md)
- [Hyperion HFM](hfm.md)
- [Cloud Applications (PCMCS/EPBCS)](cloud.md)

---

© 2025 EPMware, Inc. All rights reserved.
