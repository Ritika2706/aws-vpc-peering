# Publish the documentation site with GitHub Pages

1. Create a public GitHub repository named `aws-vpc-peering`.
2. Upload the contents of this project package (including `README.md`, the report PDF, and the `docs/` folder).
3. Open the repository's **Settings → Pages**.
4. Under the build/deployment source, choose **Deploy from a branch**.
5. Select branch `main` and folder `/docs`, then save.
6. Wait for GitHub Pages to finish deploying. The site URL will usually be:
   `https://YOUR-GITHUB-USERNAME.github.io/aws-vpc-peering/`
7. Replace `YOUR-GITHUB-USERNAME` with your actual GitHub username. Do not claim the link is live until GitHub Pages shows a successful deployment.

The documentation website is a live project page, not a public endpoint to the private AWS network. Keep App-A and App-B private.
