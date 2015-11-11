LoggerRabbitBackend
=================

A simple `Logger` backend which publishes logs to RabbitMQ.

## Configuration

`LoggerRabbitBackend` is a custom backend for the Elixir `:logger` application. As
such, it relies on the `:logger` application to start the relevant processes.
However, unlike the default `:console` backend, we may want to configure
multiple log queues, each with different log levels, formats, etc. Also, we want
`:logger` to be responsible for starting and stopping each of our logging
processes for us. Because of these considerations, there must be one `:logger`
backend configured for each log queue we need. Each backend has a name like
`{LoggerRabbitBackend, id}`, where `id` is any elixir term (usually an atom).

Our config.exs would have an entry similar to this:

```elixir
# tell logger to load a LoggerRabbitBackend processes
config :logger,
  backends: [{LoggerRabbitBackend, :error_log}]
```

With this configuration, the `:logger` application will start one `LoggerRabbitBackend`
named `{LoggerRabbitBackend, :error_log}`. We still need to set the correct file
path and log levels for the backend, though. To do that, we add another config
stanza. Together with the stanzaabove, we'll have something like this:

```elixir
# tell logger to load 2 LoggerRabbitBackend processes
config :logger,
  backends: [{LoggerRabbitBackend, :error_log}]

# configuration for the {LoggerFileBackend, :error_log} backend
config :logger, :error_log,
  path: "/var/log/my_app/error.log",
  level: :error
```

Check out the examples below for runtime configuration and configuration for
multiple log files.

`LoggerRabbitBackend` supports the following configuration values:

* path - the path to the log file
* level - the logging level for the backend
* format - the logging format for the backend
* metadata - the metadata to include


### Examples

#### Runtime configuration

```elixir
Logger.add_backend {LoggerRabbitBackend, :debug}
Logger.configure {LoggerRabbitBackend, :debug},
  path: "/path/to/debug.log",
  format: ...,
  metadata: ...
```

#### Application config for multiple log files

```elixir
config :logger,
  backends: [{LoggerRabbitBackend, :info},
             {LoggerRabbitBackend, :error}]

config :logger, :info,
  path: "/path/to/info.log",
  level: :info

config :logger, :error,
  path: "/path/to/error.log",
  level: :error
```
