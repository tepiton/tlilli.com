# mimeo

A minimal static website hosted on GitHub Pages.

## Structure

This repository contains a simple static site with the following components:

- `index.html` - Single HTML page with embedded CSS
- `.github/workflows/static.yml` - GitHub Actions workflow for automated deployment

## How It Works

### GitHub Pages Deployment

The site uses GitHub Actions to deploy static content to GitHub Pages:

1. **Trigger**: Deployment runs automatically on pushes to the `main` branch
2. **Workflow**: The `.github/workflows/static.yml` workflow handles deployment
3. **Process**:

   - Checks out the repository
   - Configures GitHub Pages
   - Uploads the entire repository as an artifact
   - Deploys to GitHub Pages

4. **Result**: Site is available at the GitHub Pages URL

### Permissions

The workflow requires specific permissions:

- `contents: read` - Read repository contents
- `pages: write` - Write to GitHub Pages
- `id-token: write` - Request OIDC JWT ID token

### Concurrency

The workflow uses concurrency controls to prevent conflicts:

- Only one deployment runs at a time
- New deployments wait for in-progress deployments to complete
- In-progress deployments are not cancelled

## Creating a Similar Site

To create a similar minimal GitHub Pages site:

### 1. Create Repository

```bash
mkdir your-site-name
cd your-site-name
git init
```

### 2. Add HTML File

Create `index.html`:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Your Site Title</title>
    <style>
      /* Your CSS here */
    </style>
  </head>
  <body>
    <!-- Your content here -->
  </body>
</html>
```

### 3. Add GitHub Actions Workflow

Create `.github/workflows/static.yml`:

```yaml
name: Deploy static content to Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Setup Pages
        uses: actions/configure-pages@v5
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: '.'
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### 4. Push to GitHub

```bash
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin git@github.com:username/repo-name.git
git push -u origin main
```

### 5. Enable GitHub Pages

1. Go to repository Settings
2. Navigate to Pages section
3. Under "Build and deployment", select "GitHub Actions" as the source
4. The workflow will deploy automatically on the next push

## Benefits of This Approach

- **Minimal**: No build process, frameworks, or dependencies
- **Fast**: Direct deployment of static files
- **Automated**: Deploys automatically on push to main branch
- **Simple**: Easy to understand and maintain
- **Flexible**: Can add additional static files (CSS, JS, images) as needed

## Notes

- The workflow uploads the entire repository root directory
- All files in the repository will be publicly accessible via GitHub Pages
- No build step or static site generator is required
- Can be manually triggered via the Actions tab using `workflow_dispatch`
