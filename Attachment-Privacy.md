_Doc Attachments_ inherit the privacy levels of the Docs that they are attached to. This works by redirecting the normal WordPress attachment links to a special script that checks for a user's capability before delivering the file.

BuddyPress Docs attempts to take additional steps to ensure that your Docs cannot be accessed directly by those who might guess the URLs. Depending on your server setup, you may have to take some additional manual steps to ensure 100% attachment privacy.

## Apache

On Apache installations, BuddyPress Docs attempts to protect attachments on a doc-by-doc basis. This means that attachments to private Docs will be served through BuddyPress - ensuring proper authorization - while attachments to public Docs will be served directly by Apache - ensuring the lowest possible overhead.

In order for BuddyPress Docs to protect attachments in this way, `AllowOverride` must allow for subdirectory-specific rewrites. The vast majority of WP installations will have this setting enabled by default. Contact your host for details. For more information, see <a href="https://httpd.apache.org/docs/current/mod/core.html#allowoverride">Apache's documentation for `AllowOverride`</a>.

## nginx

If you're running nginx, directory-specific access settings are configured in a global configuration file (often `/etc/nginx/nginx.conf`, but possibly a sub-config file that is specific to your site). To prevent direct access to Docs attachments, use a directive like the following:

    location /wp-content/uploads/bp-attachments/ {
        rewrite ^.*uploads/bp-attachments/([0-9]+)/(.*) /?p=$1&bp-attachment=$2 permanent;
    }

This rule assumes that nginx sees your WP installation at `/`. If this is not the case, you may have to adjust these paths.

*Performance note*: This modification will force all Doc Attachment downloads to pass through WordPress, instead of being served by nginx, even if the associated Doc is public. On very high-traffic sites, this could cause performance issues. If you do not have any non-public Docs, you may consider skipping the directive suggested above, and allowing nginx to serve all Docs Attachments directly.

## IIS7

IIS7 rewrite rules are controlled by a Web.config file, typically in your WordPress site root. To prevent direct access to Docs attachments, use a directive like the following:

    <rule name="buddypress-docs-attachments">
        <match url="^/wp-content/uploads/bp-attachments/([0-9]+)/(.*)$"/>
            <conditions>
	        <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="false"/>
	    </conditions>
        <action type="Redirect" url="?p={R:1}&bp-attachment={R:2}"/>
    </rule>

The specifics of the rule may vary depending on your configuration (whether you're running Multisite, etc).

*Performance note*: This modification will force all Doc Attachment downloads to pass through WordPress, instead of being served by IIS, even if the associated Doc is public. On very high-traffic sites, this could cause performance issues. If you do not have any non-public Docs, you may consider skipping the directive suggested above, and allowing IIS to serve all Docs Attachments directly.
