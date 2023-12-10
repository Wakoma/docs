# Add your own service

In a nutshell, the all services are just a docker compose file that is rendered
into the target machine through jinja2 filters. Variables into jinja context
are taken from ansible and `<role>/defaults/main.yml` and from `hosts/your-server>.yml`.

If you would need more information then you can even send new variables into the context
from your custom `<role>/tasks/main.yml` file is able to remotely execute commands and 
record their output. So you can do pretty much anything.

The basic structure for a service can be found in `roles/lokal/example` with explanations.
The most complicated thing is not to expose ports to public internet but to set
traefik labels correctly so you expose your service to the public internet. 