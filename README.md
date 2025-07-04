# Fastly Dashboards

A comprehensive, out-of-the-box monitoring and alerting solution for Fastly services.

This repository contains a [Docker Compose][compose] setup that deploys a full monitoring stack, including: [Prometheus Fastly exporter][fastly-exporter], [Prometheus][prom], [Alertmanager][alertmanager], and [Grafana][grafana]. It comes pre-loaded with a suite of dashboards and alerting rules, with built-in [Slack][slack] integration.

## Features

- **Turnkey Setup**: Launch a complete Fastly monitoring stack with a single command.
- **Comprehensive Dashboards**: Visualize real-time and historical metrics with a rich set of pre-built Grafana dashboards.
- **Extensive Alerting**: Over 70 pre-configured Prometheus alerts to proactively monitor service health, performance, and security.
- **Slack Integration**: Receive timely alerts directly in your Slack workspace.
- **Customizable**: Easily extend the dashboards and alerting rules to fit your specific needs.

## Dashboards

The following Grafana dashboards are provisioned automatically:

- **Home**: A landing page with links to all other dashboards.
- **Fastly Dashboard**: A high-level overview of all Fastly services.
- **Fastly Service**: A detailed view of a single Fastly service.
- **Fastly Compute**: Metrics for your Fastly Compute services.
- **Fastly Security**: Security-related metrics, including WAF and TLS data.
- **Fastly Thresholds**: A view for monitoring against defined thresholds.
- **Top Services**: A summary of your most active services.
- **Top Datacenters**: A breakdown of traffic by Fastly datacenter.
- **Top Origins**: A summary of your most active origin servers.

## Alerting

The stack includes a set of pre-configured Prometheus alerting rules that are sent to Alertmanager and can be routed to Slack. The rules cover the following categories:

- Account
- Cache Performance
- Compute
- Datacenter Health
- Errors
- Latency
- Origin Health
- Security
- Service Health
- Thresholds
- Traffic Volume

You can customize and add your own rules in the [`prometheus/rules/`](prometheus/rules/) directory.

## Stack Components

This project uses the following containerized services:

| Service         | Image                            | Version   |
| :-------------- | :------------------------------- | :-------- |
| Prometheus      | `prom/prometheus`                | `v2.37.0` |
| Alertmanager    | `prom/alertmanager`              | `v0.24.0` |
| Grafana         | `grafana/grafana`                | `9.1.3`   |
| Fastly Exporter | `ghcr.io/fastly/fastly-exporter` | `v7.2.4`  |
| Envsubst        | `bhgedigital/envsubst`           | `latest`  |

## Getting Started

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/) and [Docker Compose](https://docs.docker.com/compose/install/)
- A [Fastly API Token][fastly-token] with `global:read` permissions.
- A [Slack Incoming Webhook URL][slack-webhook] for alerts.

### Configuration

1. Clone the repository:

   ```bash
   git clone https://github.com/fastly/fastly-dashboards.git
   cd fastly-dashboards
   ```

2. Export the required environment variables:

   ```bash
   export FASTLY_API_TOKEN="YOUR_FASTLY_TOKEN"
   export SLACK_API_URL="YOUR_SLACK_WEBHOOK_URL"
   export SLACK_CONFIG_CHANNEL="#your-slack-channel"
   ```

### Running the Stack

Use `docker compose` (recommended) or `docker-compose` to launch the stack:

```bash
docker compose up -d
```

It may take a few minutes for all services to start and for data to be collected.

### Running with containerd & nerdctl

If you are using `nerdctl`, you may need to work around the lack of support for environment variable interpolation.

```bash
env > .env
nerdctl run envsubst
nerdctl compose up
```

## Accessing Grafana

Once the stack is running, you can access the Grafana dashboards at [http://localhost:3000](http://localhost:3000).

Login is disabled, and you will be granted anonymous admin access.

## Troubleshooting

1. **`no configuration file provided: not found`**

   This can happen if you are using Docker Snap, which requires that all files Docker needs access to live within your `$HOME` folder. Try running the project from a directory inside your home folder.

2. **Graphs are broken and my system is dying!**

   Processing Fastly metrics can be resource-intensive, especially with many services. To reduce the load, you can configure the `fastly-exporter` to sample a fraction of your services using the `FASTLY_EXPORTER_OPTIONS` environment variable.

   For example, to process metrics for 1/10th of your services:

   ```bash
   export FASTLY_EXPORTER_OPTIONS="-service-shard 1/10"
   docker compose up
   ```

3. **No Slack Alerts**

   If you see an error like `channel \"#NO_SLACK_CONFIG_CHANNEL\": unexpected status code 404: 404 page not found`, it means your Slack integration is not configured correctly.

   Ensure that the `$SLACK_API_URL` and `$SLACK_CONFIG_CHANNEL` environment variables are exported correctly before starting the stack.

---

## Credit

The official dashboards were inspired by the original [fastly-dashboards] project by [@mrnetops]. Their work was featured in the [Magic tricks with Docker (or how to monitor Fastly in about five minutes)][altitude-2020-video] presentation at Fastly Altitude 2020.

[![Magic tricks with Docker (or how to monitor Fastly in about five minutes)](/images/Fastly-Altitude-2020.jpeg)][altitude-2020-video]

[fastly-dashboards]: https://github.com/mrnetops/fastly-dashboards#
[compose]: https://github.com/docker/compose
[fastly-exporter]: https://github.com/peterbourgon/fastly-exporter
[fastly]: https://www.fastly.com
[fastly-token]: https://docs.fastly.com/en/guides/finding-and-managing-your-api-token
[prom]: https://prometheus.io
[grafana]: https://grafana.com
[alertmanager]: https://prometheus.io/docs/alerting/latest/alertmanager/
[slack]: https://www.slack.com
[slack-webhook]: https://api.slack.com/messaging/webhooks
[altitude-2020-video]: https://vimeo.com/480545143
[@mrnetops]: https://github.com/mrnetops
