# OPE Custom Alertmanager Templates

This Repository will be the version control system for the custom alertmanager templates that we create for prometheus. To support the custom alerting efforts we have created custom templates that allow recipients to glean as much information as possible with as little reading as possible. So far we have only implemented this for emails. but going forward we may do this for slack, pagerduty etc. 

## Custom Email Templates Requirements

* Informative Email Subject Lines. 
* It is necessary to see the origination of the error (foundation, component, job, etc) in the subject line and again in the body of the email. 
* A link in the body of the email to the alertmanager in case further information is required. 

## Creating Custom Email Templates

In Prometheus and Alertmanager templates are created using the golang templating format. This can be quite tricky to get the hang off at first. Please see [Golang Templating Documentation](https://golang.org/pkg/text/template/) for more information.

Templates can be created in files can either be of type .html or .tmpl. I have created a bosh release with the custom template files that can be deployed to the pcf platform.

## Annotated Email Subject Line Template

```
{{/* The first Line below Defines a Template Named "vm_health_subject.tmpl" */}}
{{ define "vm_health_subject.tmpl" }}

{{/* The line below prints the value of Status in Upper Case and if it equals firing prints out the length of the array Firing e.g. "[FIRING:1]-"*/}}
[{{ .Status | toUpper }}{{ if eq .Status "firing" }}:{{ .Alerts.Firing | len }}{{ end }}]-

{{/* The Block Below Prints alertname then checks if it equals a certain value. If it does it prints out more values. If not it skips this block. e.g. " ADD Email SUbkect Line" */}}
{{- .GroupLabels.alertname }},{{ if eq .GroupLabels.alertname "BOSH_System_VM_Down" }}{{ .CommonLabels.SortedPairs.bosh_job_ip }},{{ .CommonLabels.SortedPairs.bosh_job_name }}/{{ .GroupLabels.SortedPairs.bosh_job_index }},{{ end }}

{{/* The first below prints value of Label Named Environment e.g. "cf_lab_ash" */}}
{{- .CommonLabels.environment }},

{{/* The first below prints value of Label Named Service e.g. "p-redis" */}}
{{- .CommonLabels.service }},

{{/* The first below prints value of Label Named Severity in Uppercase e.g. "WARNING" */}}
{{- .CommonLabels.severity | toUpper }}
{{ end }}
```

When this is all put together it results in a subject line like:

A similar approach can be used with HTML to customize the body of the email. 
