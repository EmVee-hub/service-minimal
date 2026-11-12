# Container security PoC

Build and scan final service images with Grype, project VEX and the shared
**CISA BOD 26-04 decision-table-first** policy. Its configured EPSS percentile
quartile amplifier runs after BOD, with KEV and urgent 3D/3DF guards. The
5%/10% probability override is no longer active.

| CI image | Dockerfile | Base image | VEX |
| --- | --- | --- | --- |
| nginx-alpine | Dockerfile | nginx:stable-alpine | vex/openvex.json |
| nginx-debian | Dockerfile.nginx | nginx:stable | vex/openvex.json |
| php-fpm | Dockerfile.php | php:8.4-fpm-bookworm | vex/no-exceptions.openvex.json |
| redis | Dockerfile.redis | redis:7-bookworm | vex/no-exceptions.openvex.json |
| cae-live | Dockerfile.cae-live | See its Dockerfile | vex/cae-live.openvex.json |

The previous service-minimal-debian job already used nginx:stable, not a bare Debian image. Its new name makes the service explicit.

Grype scans packages present in each final built image, including inherited OS packages and service dependencies. It does not scan every package available in the Debian repositories. A normal FROM inherits the base filesystem; multi-stage builds can copy selected artifacts into a smaller final image. Renaming jobs does not remove OS findings or establish whether vulnerable code is used.

PHP and Redis start with no VEX exceptions. The nginx-specific non-applicability assertions must not be reused for other services without review. All jobs use enforce mode and upload assessments even when the policy blocks an image. Exposure is unknown (conservatively treated as public) except cae-live, which is explicitly public. CI does not deploy these services.

These are minimal upstream service image examples, not configured production applications. Tags float so subsequent builds can contain different package versions and findings; assessment artifacts describe each scan.
