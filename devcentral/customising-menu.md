# Customising Menu

This guide will walk you through the process of adding new menu items and modifying dashboard URLs in the  DevCentral.

### Adding New Menu Items

To add a new menu item, follow these steps:

1. Open the `_config.yml` file in your dev central repository, typically sfops-dev-central in your GitHub Organisation.
2. Locate the `menu` section in the file. It should look similar to this:

```yaml
menu:
  - title: Dashboards
    icon: fas fa-chart-line
    submenu:
      - name: CI/CD Performance
        link: "#cicd"
        iframeZoom: 1
      - name: Package Evolution
        link: "#evolution"
        url: "packageVisualisation/index.html"
      - name: Platform Overview
        link: "#platformOverview"
      - name: Package Metrics
        link: "#packageSummary"
  # ... rest of the menu items ...
```

3. To add a new top-level menu item, create a new item under the `menu` section with the following properties:
   * `title`: The text that appears in the menu for this item.
   * `icon`: The Font Awesome icon class to be displayed next to the menu item.
   * `submenu`: An array of submenu items for this menu item.
4. To add a new submenu item, create a new item under the `submenu` array of the desired top-level menu item with the following properties:
   * `name`: The text that appears in the submenu for this item.
   * `link`: The unique identifier for the submenu item, prefixed with `#`.
   * `url` (optional): The URL or path to the page or resource associated with this submenu item.
   * `iframeZoom` (optional): The zoom level for the iframe displaying the page or resource.
5. Save the `_config.yml` file after making the necessary changes.

### Modifying Dashboard URLs

DevCentral allows you to specify the URLs for dashboard items directly in the `_config.yml` file. This includes the URLs for the CI/CD Performance, Platform Overview, and Package Metrics dashboards, that are iFrames from your monitoring platform such as DataDog, Splunk or NewRelic

To modify the dashboard URLs, follow these steps:

1. Open the `_config.yml` file in your project.
2. Locate the `menu` section and find the submenu item corresponding to the dashboard you want to modify.
3. Update the `url` property of the submenu item with the desired URL. For example, to change the CI/CD Performance dashboard URL:

```yaml
menu:
  - title: Dashboards
    icon: fas fa-chart-line
    submenu:
      - name: CI/CD Performance
        link: "#cicd"
        url: "https://your-custom-url.com/cicd-performance"
        iframeZoom: 1
```

4. Save the `_config.yml` file after making the necessary changes.

{% hint style="info" %}
It's important to note that you should not modify the URLs for existing pages that are provided out of the box. These pages are an integral part of the SFOps Dev Central application, and changing their URLs may cause unexpected behavior or broken links. Only update the URLs for dashboard items that you want to customize.
{% endhint %}

