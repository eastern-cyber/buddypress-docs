_Doc Attachments_ inherit the privacy levels of the Docs that they are attached to. This works by redirecting the normal WordPress attachment links to a special script that checks for a user's capability before delivering the file.

BuddyPress Docs attempts to take additional steps to ensure that your Docs cannot be accessed directly by those who might guess the URLs. Depending on your server setup, you may have to take some additional manual steps to ensure 100% attachment privacy.

## nginx

If you're running nginx, directory-specific access settings are configured in a global configuration file (often `/etc/nginx/nginx.conf`, but possibly a sub-config file that is specific to your site). To prevent direct access to Docs attachments, use a directive like the following:

    location /wp-content/uploads/bp-attachments/ {
        rewrite ^.*uploads/bp-attachments/([0-9]+)/(.*) /?p=$1&bp-attachment=$2 permanent;
    }

This rule assumes that nginx sees your WP installation at `/`. If this is not the case, you may have to adjust these paths.

*Performance note*: This modification will force all Doc Attachment downloads to pass through WordPress, instead of being served by nginx, even if the associated Doc is public. On very high-traffic sites, this could cause performance issues. If you do not have any non-public Docs, you may consider skipping the directive suggested above, and allowing nginx to serve all Docs Attachments directly.