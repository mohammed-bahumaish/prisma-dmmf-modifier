# Publishing to npm

Follow these steps to publish your package to npm:

## Prerequisites

1. Create an npm account if you don't have one at [npmjs.com](https://www.npmjs.com/signup)
2. If you want to publish under the `@prisma-editor` scope, you'll need to:
   - Create an organization named "prisma-editor" on npm, or
   - Change the package name in package.json to use your own scope (e.g., `@mohammed-bahumaish/prisma-dmmf-modifier`)

## Steps to publish

1. **Login to npm** from your terminal:

```bash
npm login
```

Enter your npm username, password, and email when prompted.

2. **Update the package.json** with your information:
   - Update the `author` field with your name and email
   - Update the `repository` URL with your actual GitHub repository

3. **Build the package**:

```bash
npm run build
```

4. **Publish the package**:

To publish a scoped package as public:

```bash
npm publish --access public
```

If you've already set up the organization and published before, you can simply use:

```bash
npm publish
```

## Versioning

When making updates to your package, remember to update the version number in package.json following semantic versioning:

- **Patch releases (1.0.x)**: Bug fixes not affecting the API
- **Minor releases (1.x.0)**: New features, but backwards compatible
- **Major releases (x.0.0)**: Changes that break backward compatibility

To update the version, you can use:

```bash
npm version patch # for bug fixes
npm version minor # for new features
npm version major # for breaking changes
```

## Publishing a new version

After making changes and updating the version number:

1. Build the package: `npm run build`
2. Publish: `npm publish` 