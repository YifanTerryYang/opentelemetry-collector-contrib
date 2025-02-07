# Syslog Exporter

<!-- status autogenerated section -->
| Status        |           |
| ------------- |-----------|
| Stability     | [development]: logs   |
| Distributions | [] |
| Issues        | [![Open issues](https://img.shields.io/github/issues-search/open-telemetry/opentelemetry-collector-contrib?query=is%3Aissue%20is%3Aopen%20label%3Aexporter%2Fsyslog%20&label=open&color=orange&logo=opentelemetry)](https://github.com/open-telemetry/opentelemetry-collector-contrib/issues?q=is%3Aopen+is%3Aissue+label%3Aexporter%2Fsyslog) [![Closed issues](https://img.shields.io/github/issues-search/open-telemetry/opentelemetry-collector-contrib?query=is%3Aissue%20is%3Aclosed%20label%3Aexporter%2Fsyslog%20&label=closed&color=blue&logo=opentelemetry)](https://github.com/open-telemetry/opentelemetry-collector-contrib/issues?q=is%3Aclosed+is%3Aissue+label%3Aexporter%2Fsyslog) |
| [Code Owners](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/CONTRIBUTING.md#becoming-a-code-owner)    | [@kkujawa-sumo](https://www.github.com/kkujawa-sumo), [@rnishtala-sumo](https://www.github.com/rnishtala-sumo), [@astencel-sumo](https://www.github.com/astencel-sumo) |

[development]: https://github.com/open-telemetry/opentelemetry-collector#development
<!-- end autogenerated section -->

The syslog exporter supports sending messages to a remote syslog server.

- This exporter can forward syslog messages to syslog server using [RFC5424][RFC5424] and [RFC3164][RFC3164].
- It is recommended that this syslog exporter be used with the [syslog receiver][syslog_receiver] or with [filelog receiver][filelog_receiver] along with [syslog_parser][syslog_parser] configured in the receiver, please see [examples](./examples/)
  This ensures that all the syslog message headers are populated with the expected values.
- Not using the `syslog_parser` will result in the syslog message being populated with default header values.

## Configuration

**The following configuration options are available**:

- `endpoint` - (required) syslog endpoint
- `network` - (default = `tcp`) tcp/udp
- `port` - (default = `514`) A syslog port
- `protocol` - (default = `rfc5424`) rfc5424/rfc3164
  - `rfc5424` - Expects the syslog messages to be rfc5424 compliant
  - `rfc3164` - Expects the syslog messages to be rfc3164 compliant
- `tls` - configuration for TLS/mTLS
  - `insecure` (default = `false`) whether to enable client transport security, by default, TLS is enabled.
  - `cert_file` - Path to the TLS cert to use for TLS required connections. Should only be used if `insecure` is set to `false`.
  - `key_file` - Path to the TLS key to use for TLS required connections. Should only be used if `insecure` is set to `false`.
  - `ca_file` - Path to the CA cert. For a client this verifies the server certificate. For a server this verifies client certificates. If empty uses system root CA. Should only be used if `insecure` is set to `false`.
  - `insecure_skip_verify` -  (default = `false`) whether to skip verifying the certificate or not.
  - `min_version` (default = `1.2`) Minimum acceptable TLS version
  - `max_version` (default = `""` handled by [crypto/tls][cryptoTLS] - currently TLS 1.3) Maximum acceptable TLS version.
  - `reload_interval` - Specifies the duration after which the certificate will be reloaded. If not set, it will never be reloaded.
- `retry_on_failure`
  - `enabled` (default = `true`)
  - `initial_interval` (default = `5s`): Time to wait after the first failure before retrying; ignored if `enabled` is `false`
  - `max_interval` (default = 30s): Is the upper bound on backoff; ignored if `enabled` is `false`
  - `max_elapsed_time` (default = `120s`): Is the maximum amount of time spent trying to send a batch; ignored if `enabled` is `false`
- `sending_queue`
  - `enabled` (default = `false`)
  - `num_consumers` (default = `10`): Number of consumers that dequeue batches; ignored if `enabled` is `false`
  - `queue_size` (default = `5000`): Maximum number of batches kept in memory before data; ignored if `enabled` is `false`;
  User should calculate this as `num_seconds * requests_per_second` where:
    - `num_seconds` is the number of seconds to buffer in case of a backend outage
    - `requests_per_second` is the average number of requests per seconds.
  - `storage` (default = `none`): When set, enables persistence and uses the component specified as a storage extension for the [persistent queue][persistent_queue]
- `timeout` (default = 5s) Time to wait per individual attempt to send data to a backend

Please see [example configurations](./examples/).

[RFC5424]: https://www.rfc-editor.org/rfc/rfc5424
[RFC3164]: https://www.rfc-editor.org/rfc/rfc3164
[syslog_parser]: https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/pkg/stanza/docs/operators/syslog_parser.md
[syslog_receiver]: https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver/syslogreceiver
[filelog_receiver]: https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver/filelogreceiver
[cryptoTLS]: https://github.com/golang/go/blob/518889b35cb07f3e71963f2ccfc0f96ee26a51ce/src/crypto/tls/common.go#L706-L709
[persistent_queue]: https://github.com/open-telemetry/opentelemetry-collector/blob/main/exporter/exporterhelper/README.md#persistent-queue
