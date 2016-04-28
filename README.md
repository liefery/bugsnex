# Bugsnex

API client and logger for Bugsnag

## Installation

If [available in Hex](https://hex.pm/docs/publish), the package can be installed as:

  1. Add bugsnex to your list of dependencies in `mix.exs`:

        def deps do
          [{:bugsnex, "~> 0.0.1"}]
        end

  2. Ensure bugsnex is started before your application:

        def application do
          [applications: [:bugsnex]]
        end


## Configuration

```elixir
config :bugsnex, :otp_app, :your_app_name
config :bugsnex, :api_key, "your_api_key"
config :bugsnex, :release_stage, "staging"
config :bugsnex, :use_logger, true

```

## Usage

### Sending bug reports
Once configured, use `Bugsnex.notice(exception)` or `Bugsnex.notice(exception,stacktrace)` to send errors to Bugsnag.

If `use_logger` is set to `true`, an [error logger](http://erlang.org/doc/man/error_logger.html) event handler is added
and [SASL](http://erlang.org/doc/apps/sasl/error_logging.html) compliant errors are sent to Bugsnag.

### Tracking deployments
Use `Bugsnex.track_deploy(additional_params)` to send a deployment notification to bugsnag.
`additional_params` is an optional map with keys:

  * `:apiKey`: your bugsnag api key (default is `Application.get_env(:bugsnex, :api_key)`)
  * `:repository`: url of your source code repository (default is `Application.get_env(:bugsnex, :repository_url)`)
  * `:releaseStage`: the release stage (e.g. staging, production) (default is `Application.get_env(:bugsnex, :release_stage)`)
  * `:branch`: scm branch this release was deployed from
  * `:revision`: scm revision this release was deployed from
  * `:appVersion`: SemVer version of your app


### Metadata

You can associate metadata by calling `Bugsnex.put_metadata(%{some: "metadata"})`.
Note that metadata is stored inside the [process dictionary](http://www.erlang.org/course/advanced#dict).
This means that you shouldn't put a lot of data in there and also that it's only associated with the
calling process. Errors in different processes won't be associated with that metadata.

There are some special keys for metadata that Bugsnag understands (explanations paraphrased from the Bugsnag API documentation):

  * `%{user: %{id: 123, email: "user@example.com", name: "Some name"}`: Information about the user affected by the crash
  * `%{context: "auth/session#create"}`: A string representing what was happening in the application at the time of the error
  * `%{device: %{osVersion: "2.1.1", hostname: "web1.internal"}}`: Information about the computer/device running the app
