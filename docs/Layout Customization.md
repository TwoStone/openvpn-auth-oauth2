# Layout Customization

openvpn-auth-oauth2 supports custom templates for the login page.
The template must a valid Go Template file.
After rendering, valid HTML must be returned.

The default template can be found here:
[index.gohtml](https://github.com/jkroepke/openvpn-auth-oauth2/blob/main/internal/ui/index.gohtml)

Available variables:

- `{{.title}}`: `Access denied` or `Access granted`
- `{{.message}}`: Potential error message or success message
- `{{.errorID}}`: ErrorID of an error, if present
- `{{.clientConfigProfiles}}`: List of available profiles, used to render the profile selection screen.
- `{{.token}}`: An encrypted token to identify the user session, used for profile selection.

The [go template engine](https://pkg.go.dev/text/template) is used to render the HTML file.

## Client Profile Selector

If the [Client Profile Selector](Client%20specific%20configuration.md#client-profile-selector) is enabled and multiple
profiles are available for the user, a profile selection screen is shown. The custom template must handle this case by itself.

Minimal example to render the profile selector:

```gotemplate
{{- if .clientConfigProfiles }}
<div class="profile-buttons">
  <form method="POST" action="./profile-submit" class="profile-form">
    <input type="hidden" name="token" value="{{ .token }}">
    {{- range .clientConfigProfiles }}
    <input class="profile-button" type="submit" name="profile" value="{{ . }}">
    {{- end }}
  </form>
</div>
{{- end }}
```

The default style uses CSS classes to style the profile selector:
- `profile-buttons`: Container for the profile buttons
- `profile-form`: Form containing the profile buttons
- `profile-button`: Individual profile button

## Overriding the default assets

To override the default assets, you can configure `http.assets-path` with the path to the directory containing the assets.

The default assets:

- [`style.css`](https://github.com/jkroepke/openvpn-auth-oauth2/blob/main/internal/ui/assets/style.css): CSS file to style the layout. Override can be set inside the template file as well.
- [`favicon.png`](https://github.com/jkroepke/openvpn-auth-oauth2/blob/main/internal/ui/assets/favicon.png): Favicon of the login page
- [`i18n.js`](https://github.com/jkroepke/openvpn-auth-oauth2/blob/main/internal/ui/assets/i18n.js): Localization script
- [`i18n/<lang>.json`](https://github.com/jkroepke/openvpn-auth-oauth2/tree/main/internal/ui/assets/i18n): Language specific localization file. `<lang>` stands for the language code, for example `en` for English.
  See [de.json](https://github.com/jkroepke/openvpn-auth-oauth2/blob/main/internal/ui/static/i18n/de.json) for an example.

Alternatively, you link additional assets via external locations inside your custom template.

## Custom localization

If you want to provide custom localization, you have to configure `http.assets-path` first. In the assets directory,
create a new directory named `i18n` and put your localization files in there. The filename must be the language code
followed by `.json`. For example, `en.json` for English.

Instead, providing a custom localization file locally, think about to submit a pull request to the project to provide
the localization for everyone.
