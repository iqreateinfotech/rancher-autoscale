
<h1> Autoscaling </h1>

<h2> Description </h2>

* There will be one autoscaler running per service. The autoscaler will need the cattle API keys to make changes to the scale of the service. It will have three actions for scaling:
Scale out: Increase number of containers of the service
Scale in: Decrease number of containers of the service
Default: Set to the default number of containers, with which the service was created

* The cpu shares and memory limit for every container need to be specified when it is created. If these fields are not specified then the autoscaler will not know the current % CPU utilization or % memory utilization and won’t take any action on that service. So the fields `cpu_shares` and `mem_limit` should be set for the service in `docker-compose.yml` file.

* User can add rules as following:
If average CPU utilization >= 60% for 10 minutes, scale out by 1 container
			      		   >= 85% for 5 minutes, scale out by 3 containers
			      <= 40% for 25 minutes, scale in by 2 containers
Each rule will be of the form:
`If <metric> <operation> <metric_value> <time_span> then <scale_action> <number_of_containers>`, where
<b> metric </b> can be one of the three: % CPU utilization, % memory utilization and HTTP/TCP requests/second
<b> operation </b> can be one of these: ‘<=’, ‘>=’, ‘=’, ‘<’, ‘>’
<b> metric_value </b> will be specified by the user. It will be the percentage of the threshold value of that metric, at which scale in or scale out should happen.
<b> time_span </b> is the time for which the containers need to have the `<metric_value>` before scale in or scale out takes place. Time span for scale out operations should be more than that for scale in.
<b> scale_action </b> can be one of these: Scale out, Scale in, Default (set to original number of containers)
<b> number_of_containers </b> is the change in number of containers.

* Autoscaler can obtain CPU and memory utilization through API, and get the HTTP request rate from HAProxy Frontend Metrics
