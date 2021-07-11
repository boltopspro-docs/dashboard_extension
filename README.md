<!-- note marker start -->
**NOTE**: This repo contains only the documentation for the private BoltsOps Pro repo code.
Original file: https://github.com/boltopspro/dashboard_extension/blob/master/README.md
The docs are publish so they are available for interested customers.
For access to the source code, you must be a paying BoltOps Pro subscriber.
If are interested, you can contact us at contact@boltops.com or https://www.boltops.com

<!-- note marker end -->

# Dashboard Extension

[![BoltOps Badge](https://img.boltops.com/boltops/badges/boltops-badge.png)](https://www.boltops.com)

This is a [lono extension](https://lono.cloud/docs/extensions/) that provides a `Dashboard::Builder` class and some helpers that makes building CloudWatch Dashboards easy.

## Helper Methods

Name | Description
-- | ---
metric_widget | Generates common metric widget. The most used widget.
single_value_metric | Generates single value widget. Example: A counter with the current number of requests.
text_widget | Generates text widget. Useful for informative messages.

## Usage

app/blueprints/demo/templates/demo.rb:

```ruby
extend_with "dashboard_extension"
dashboard
```

app/blueprints/demo/helpers/dashboard_helper.rb:

```ruby
module DashboardHelper
  @@aws_region = AwsData.new.region

  def dashboard
    # Note: CloudFormation generates a name based on the logical id. IE: Dashboard-xBxd6L5oNzoV
    resource("Dashboard", "AWS::CloudWatch::Dashboard",
      DashboardName: ref("DashboardName", Conditional: true), # no spaces allowed
      DashboardBody: sub(JSON.pretty_generate(
        {widgets: widgets}
      ))
    )
  end

  def widgets
    dashboard = Dashboard::Builder.new
    dashboard.add_row(elb_widget)
    dashboard.widgets
  end

  def elb_widget
    metric_widget(
      metrics: [
        [
          "AWS/ApplicationELB",
          "HTTPCode_ELB_5XX_Count",
          "LoadBalancer",
          "${LoadBalancerId}"
      ],
      title: "ELB 5xx Errors"
    )
  end
end
```

## Installation

Add this line to the Gemfile:

    gem "dashboard_extension"

And then execute:

    bundle
