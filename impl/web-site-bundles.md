# Implementation - Web Site - Bundles

The Web Site has the following dependencies on "client-side" external packages:

- bootstrap
- jquery
- jquery-validation
- jquery-validation-unobtrusive
- popper
- Kendo UI

## Bundling

These packages - except Kendo UI - are updated using `LibraryManager` and bundled using `BundlerMinifier`.

The following packages are required in WebApi.csproj:

- `Microsoft.Web.LibraryManager.Build`
- `BuildBundlerMinifier`

`LibraryManager` is configured in `libman.json` and downloads the packages to a folder named `_libman`.

`BundlerMinifier` is configured in `bundleconfig.json` and bundles the CSS files to `wwwroot\css\*` and the JS files `wwwroot\lib\*`.

> The two processes are configured to run at every build.

## Kendo UI

Kendo, on the other hand, is bundled manually and placed in `wwww\lib\kendo-ui\*`.

## Custom CSS and Custom JS

The specific styles and scripts for IDS are in `site.css` and `site.js`.