# spring-boot-actuator
Demo of spring-boot-actuator


# Task 1 :  CI/CD Pipeline

Managed to make CI work build,create docker,push to ecr. But couldn't test it on aws eks with CD part.

 - Jenkinsfile - Available at main directory

 - Dockerfile -  Available at infra/  , Exposes 9090 port

 - Deployment,service manifest - Available under deployment/deployment.yaml
    - Deployment port - 9090
    - Service port - 80->9090

 - Ingress - Available under istio/
   - New NS and gateway and Virtual service for rule /actutor/health -> serviceName : 8080
   ref : https://istio.io/latest/docs/tasks/traffic-management/ingress/ingress-control/

 - Horizontal Pod AutoScaling: Note I've added a CPU scaling in deployment.yaml. 
   - One of way to achieve HPA based on requests/min.
     - https://dev.to/mraszplewicz/horizontal-pod-autoscaling-based-on-http-requests-metric-from-istio-cc3
     - https://itnext.io/autoscaling-apps-on-kubernetes-with-the-horizontal-pod-autoscaler-798750ab7847
   Since our requirement is requests/min which is custom metric we can achieve that with above guide.

 - Probes : livenessProbe and redinessProbe, covers below two of the requirements. We can always update initialDelaySeconds: as per application.
 	
 - Make sure that the application is up only when it is ready. Until it is ready there should be no traffic routed to it. - readinessProbe
&&	
 - If the application goes down, then Kubernetes should automatically restart the application -livenessProbe


Part 2 : 
 Plan for Migrating EKS to GKE 
