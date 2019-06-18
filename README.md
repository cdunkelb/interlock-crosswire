# interlock-crosswire

This repo intends to recreate an issue where https requests are arriving at the unintended container. All containers are being run in Interlock ssl-passthrough mode. The thought was that multiple requests for different urls were being sent through the same TLS connection (Since the URLs share the same IP from the Browser's point of view)

# Setup
You will need external DNS and your own domain to make this work.

- Create a certificate with the SAN `cat.<yourdomain>` and `dog.<yourdomain>`. The real scenario is sharing the same certificate key pair.
- Edit `dog/index.html` and `cat/index.html` to point to your domain and interlock port.
- Rebuild the images and push them to docker hub.
- Edit the `docker-compose.yml`
  - change the image names to the ones you have built and pushed to docker hub
  - change the secrets section to point to the certificates you generated for `cat.<yourdomain>` and `dog.<yourdomain>
- Deploy the stack with `docker stack deploy -c docker-compose.yml animals`

# Results
If everything is working as designed:
- The dog container should show a picture of a cat sourced the cat conatiner and perform a javascript request to the cat container printing out the contents of cat.txt to the developer console.
- The cat container should show a picture of a dog sourced from the dog container and perform a javascript request to the dog container that prints the content of cat.txt to the developer console.

# Behavior trying to recreate
The problem behavior that is seen on the user's side is that the subsequent requests on the page to another interlock balanced container arrive at the unintended container.
