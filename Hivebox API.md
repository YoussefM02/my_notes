![[hivebox-architecture-a7fe504c22027e87b6f7b188cd57d2d8.png]]

TO-DO
- Code:
	- [ ] Endpoints:
		- [x] `/version`: 
			- [x] Return app version
		- [ ]  `/temperature`: 
			- [ ] Return current average temperature based on all senseBox data
			- [x] Ensure that the data is no older 1 hour
			- [ ] Add "status" field based on the temperature average value.
				- Less than 10: Too Cold
				- Between 11-36: Good
				- More than 37: Too Hot
		- [ ] `/metrics`: 
			- [ ] Return default Prometheus metrics about the app.
	- [ ] The senseBox should be configurable via env vars
	- [ ] CI:
		- [ ] Create a GitHub Actions workflow for CI:
			- [ ] Add step to lint code and Dockerfile
			- [ ] Add step to build the Docker image
			- [ ] Add step to unit tests
			- [ ] Setup [OpenSSF Scorecard GitHub Action](https://securityscorecards.dev/#using-the-github-action) and fix any issues reported by it.
			- [ ] In the CI pipeline, call the `/version` endpoint and ensure that it returns the correct value. <------
	- [ ] Testing:
		- [ ] Write unit tests for all endpoints
		- [ ] Integration tests
---




- Infrastructure:
	- Containers:
		- Create Dockerfile
	- Manual provisioning:
		- Spin up 3 debian servers on proxmox locally
		- Deploy a local kubernetes cluster (k8s or k3s)
	- Future plans
		- Use Terraform for the server' provisioning
		- Use ansible for the kubernetes cluster