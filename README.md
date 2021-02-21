# Skupper simple nginx example

## Step 1:
Create the namespace for your deployment.

## Step 2:
   ```bash
   git clone https://github.com/ajssmith/skupper-example-nginx.git
   ```

## Step 3: 
Deploy nginx 
   ```bash
   kubectl apply -f deploy.yaml
   ```

To see the deployment pods
   ```bash
   kubectl get pods
   ```

And to retrieve the IP addresses for the pods to curl to
   ```bash
   kubectl get pods -l app=nginx -o custom-columns=ip:.status.podIP
   ```

## Step 3: 
Deploy a pod to be curl client
   ```bash
   kubectl run curl --image=radial/busyboxplus:curl -i --tty
   ```

At the command prompt, curl the IP addresses from above such as:
   ```bash
   curl 172.17.0.3
   curl 172.17.0.4
   ```
You should see returned the 'Welcome to nginx!' response

If you exit this pod you can re-attach with
   ```bash
   kubectl attach curl -c curl -i -t
   ```

## Step 4: 
Deploy skupper in the namespace
   ```bash
   skupper init
   ```

And to see the skupper-router and skupper-service-controller
   ```bash
   kubectl get pods
   ```

## Step 5: 

Skupper-ize the nginx deployment by creating a service and binding to nginx deployment

   ```bash
   skupper service create my-nginx http
   skupper service bind my-nginx deployment nginx-deployment
   ```

You should see that a service was created named my-ginx

   ```bash
   kubectl get service my-nginx -o yaml
   ```
Note in the spec for the service, the selector is for the skupper-router and its target port.

## Step 6: 

Using the curl pod from above, curl the my-ginx service rather than the pod ips (you may need to re-attach)

   ```bash
   curl my-nginx
   ```
Note that this request will go via the service to the skupper-router for the response

## Step 7: 

If you are on mini-kube and would like to reach the service on your host

   ```bash
   kubectl port-forward service/my-ginx 8080:80
   ```
And from a host terminal, curl the service

   ```bash
   curl localhost:8080
   ```