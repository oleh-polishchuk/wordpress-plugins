# wordpress-plugins
Useful wordpress plugins

### Easy WP SMTP 
https://wordpress.org/plugins/easy-wp-smtp/

    Easy WP SMTP allows you to configure and send all outgoing emails via a SMTP server.

### Contact Form 7
https://wordpress.org/plugins/contact-form-7/advanced/

    Contact Form 7 can manage multiple contact forms, plus you can customize the form.

### Contact Form 7 Captcha
https://wordpress.org/plugins/contact-form-7-simple-recaptcha/

    This plugin implements “I’m not a robot” checkbox.

### Bulk Delete 
https://wordpress.org/plugins/bulk-delete/

    Plugin allows you to delete posts, pages, attachments, users and meta fields in bulk based on different conditions and filters.

### Revisr 
https://wordpress.org/plugins/revisr/

    Revisr allows you to manage your WordPress website with a Git repository.

### Yoast SEO
https://wordpress.org/plugins/wordpress-seo/

    Yoast SEO help with your search engine optimization.

### C3 Cloudfront Cache Controller
https://wordpress.org/plugins/c3-cloudfront-clear-cache/

    This is simple plugin that clear all cloudfront cache if you publish posts.

### Advanced Custom Fields
https://wordpress.org/plugins/advanced-custom-fields/

    Our field builder allows you to quickly and easily add fields to WP edit screens with only the click of a few buttons.

### Header and Footer Scripts
https://wordpress.org/plugins/header-and-footer-scripts/

    Allow to to insert CSS and JavaScript codes to <head> or before </body>.

### WP Migrate DB
https://wordpress.org/plugins/wp-migrate-db/

    WP Migrate DB exports your database as a MySQL data dump, does a find and replace on URLs and file paths.

### WP Child Theme Generator
https://wordpress.org/plugins/wp-child-theme-generator/

    WP Child Theme Generator is a plugin to create a child theme for any theme supported by WordPress CMS.

### Menu Per Pages
https://wordpress.org/plugins/menu-per-pages/

    Wp Menu Per Pages Plugin let you provide to access a menu on your selected page.

### File Manager
https://wordpress.org/plugins/wp-file-manager/

    FILE MANAGER PROVIDES YOU FEATURES TO EDIT, DELETE, UPLOAD, DOWNLOAD, COPY AND PASTE FILES AND FOLDERS. YOU CAN EASILY COPY, MOVE FILES FOLDER OR ANY FILES FROM ONE LOCATION TO ANOTHER LOCATION.

### BackWPup
https://wordpress.org/plugins/backwpup/

    The backup plugin BackWPup can be used to save your complete installation including /wp-content/ and push them to an external Backup Service, like Dropbox, S3, FTP and many more, see list below.

### Polylang
https://wordpress.org/plugins/polylang/

    Polylang allows you to create a bilingual or multilingual WordPress site.

### Blank Slate
https://wordpress.org/plugins/blank-slate/

    This creates a page template giving you a blank page so only the content of the page is displayed.

# Wordpress Fixes

## Fix CORS issue

Add this to .htaccess file

    # BEGIN WordPress
    
    <IfModule mod_rewrite.c>
    
        RewriteEngine On
        RewriteBase /
        RewriteRule ^index\.php$ - [L]
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule . /index.php [L]

        # Restrict dir which conrains mysql dumps
        RewriteRule ^wp-content/releases/ - [L,R=404]
        RewriteRule eb_builds - [L,R=404]
        RewriteRule \.ebextensions - [L,R=404]

        <FilesMatch "\.(ttf|ttc|otf|eot|woff|woff2|font.css|css|js)$">
            Header set Access-Control-Allow-Origin "*"
        </FilesMatch>
        
    </IfModule>

    # END WordPress

## Allow CloudFront to cache main/all pages

Add this into the functions.php in child theme

    /**
     * Allow CloudFront to cache main page (or all pages)
     */
    function varnish_safe_http_headers() {
        header( 'X-UA-Compatible: IE=edge,chrome=1' );
        session_cache_limiter('');
        header("Cache-Control: public, s-maxage=120");
        header("Pragma: public, s-maxage=120");
        if( !session_id() )
        {
            session_start();
        }
    }
    add_action( 'send_headers', 'varnish_safe_http_headers' );

If this doesn't help, you can also set headers in `.htaccess` file:

    Header set Cache-Control "max-age=2592000, public"
 
## Redirect to www with .htaccess

Add this into .htaccess:

    <IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteCond %{HTTP_HOST} !^www\. [NC]
    RewriteRule ^(.*)$ http://www.%{HTTP_HOST}/$1 [R=301,L]
    </IfModule>
    
## Catch Contact Form 7 Submit event

Add this into function.php

    add_action( 'wp_footer', 'mycustom_wp_footer' );
     /**
     * Method that replace on_sent_ok with DOM Events for Contact Form 7
     *
     * @link https://contactform7.com/2017/06/07/on-sent-ok-is-deprecated/
     */
    function mycustom_wp_footer() {
        ?>
        <script type="text/javascript">

            /**
             * Method add gtm event when Newsletter Registration form will be submitted
             *
             * @page /newsletter
             */
             document.addEventListener( 'wpcf7mailsent', function( event ) {
                var contactFormId = parseInt(event.detail.contactFormId);
                 console.log('==> Contact Form 7 with id:', contactFormId, 'was submitted.');
                 switch (contactFormId) {
                    case 553:
                        addContactUsEvent(event);
                        break;
                    case 931:
                        addJobsFormEvent(event);
                        break;
                    case 1150:
                        addNewsletterRegistration(event);
                        break;
                    default:
                        console.log('==> No event found for form with id:', contactFormId);
                }
            }, false );
        </script>
        <?php
    }

## Update wordpress admin password

To show all users:

    SELECT * FROM maya1_hosting_perel_io.wp_users;

To update user password with ID = 1

    UPDATE maya1_hosting_perel_io.wp_users
    SET user_pass = '$P$BlGEKTUOu/VBJgpwrVmi7OtbtU9Dfv'
    WHERE ID = 1
    
## How to fix "WordPress Connection Information" on WP that is running in a docker container ?

Open wp-config.php with your text editor of choice and add the following line to the end of the file:

    define('FS_METHOD','direct');

