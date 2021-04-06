---
title: WooCommerce Custom Theme
date: '2020-11-16 12:41'
date_updated: 2020-11-17 09:23:05
---
WooCommerce looks great with [all WordPress themes as of version 3.3](https://woocommerce.wordpress.com/2017/12/09/wc-3-3-will-look-great-on-all-the-thees/), even if they are not WooCommerce-specific themes and do not formally declare support. Templates render inside the content, and this keeps everything looking natural on your site.
Non-WooCommerce themes, by default, also include:

* Zoom feature enabled - ability to zoom in/out on a product image
* Lightbox feature enabled - product gallery images pop up to examine closer
* Comments enabled, not Reviews - visitors/buyers can leave comments, same as a post, and not product ratings or reviews
 
 
If you want more control over the layout of WooCommerce elements or full reviews support your theme will need to integrate with WooCommerce. There are a few different ways you can do this, and they are outlined below.

## Theme Integration
There are three possible ways to integrate WooCommerce with a theme. If you are using WooCommerce 3.2 or below you will need to use one of these methods to ensure WooCommerce shop and product pages are rendered correctly in your theme. If you are using a version of WooCommerce 3.3 or above you only need to do a theme integration if the automatic one doesn't meet your needs.

### Using woocommerce_content()
This solution allows you to create a new template page within your theme that is used for **all WooCommerce taxonomy and post type displays**. While it is an easy catch-all solution, it does have a drawback in that this template is used for **all WC** taxonomies (product categories, etc.) and post types (product archives, single product pages). Developers are encouraged to use the hooks instead.

To set up this template page:

**1. Duplicate page.php**
Duplicate your theme's page.php file, and name it woocommerce.php. This file should be found like this: wp-content/themes/YOURTHEME/woocommerce.php. Then open it

**2. Replace the loop**
Next you need to find the loop (see The_Loop). The loop usually starts with a:

`<?php if ( have_posts() ) :`

and usually ends with:

`<?php endif; ?>`

This varies between themes. Once you have found it, delete it. In its place, put:

`<?php woocommerce_content(); ?>`

This will make it use **WooCommerce's loop instead**. Save the file. You're done.

**Note**: When creating woocommerce.php in your theme's folder, you will not be able to override the woocommerce/archive-product.php custom template as woocommerce.php has priority over archive-product.php. This is intended to prevent display issues.

### Using Hooks
The hook method is more involved, but it is also more flexible. This is similar to the method we use when creating themes. It's also the method we use to integrate nicely with 2010 to 2016 themes.

Insert a few lines in your theme's functions.php file.

First unhook the WooCommerce wrappers:

```
remove_action( 'woocommerce_before_main_content',
woocommerce_output_content_wrapper', 10);
remove_action( 'woocommerce_after_main_content',
'woocommerce_output_content_wrapper_end', 10);
```

Then hook in your own functions to display the wrappers your theme requires:

```
add_action('woocommerce_before_main_content',
'my_theme_wrapper_start', 10);
add_action('woocommerce_after_main_content',
'my_theme_wrapper_end', 10);
 
function my_theme_wrapper_start() {
echo '<div class='col-md-4 col-sm-6 col-xs-12
text-center> ';
}
function my_theme_wrapper_end() {
echo '</div>';
}
```

Make sure that the markup matches that of your theme. If you're unsure of which classes or IDs to use, take a look at your theme's page.php for guidance.
 
Whenever possible use the hooks to add or remove content. This method is more robust than overriding the templates. If you have overridden a template, you have to update the template any time the file changes. If you are using the hooks, you will only have to update if the hooks change, which happens much less frequently.

### Using Template Overrides
For information about overriding the WooCommerce templates with your own custom templates read the Template Structure section below. This method requires more maintenance than the hook-based method, as templates will need to be kept up-to-date with the WooCommerce core templates.

#### Custom Loop
You should be able to access products through the loop, setting the post_type arg to product:

```
<?php
 
// Setup your custom query
$args = array( 'post_type' => 'product', ... );$loop = new WP_Query( $args );
 
while ( $loop->have_posts() ) : $loop->the_post(); ?>
 
    <a href='<?php echo get_permalink( $loop->post->ID ) ?>'>
        <?php the_title(); ?>
    </a>
 
<?php endwhile; wp_reset_query(); // Remember to reset ?>
```

##### Gettting Products that have Children
```
<?php
$args = array(
    'post_type' => 'product',
    'post_status' => array('publish'),
    'posts_per_page' => -1,
    'order' => 'ASC',
);
 
$loop = new WP_Query($args);
 
$products = [];
while ($loop->have_posts()) : $loop->the_post();
    $_product = wc_get_product($id);
    if ($_product->has_child()) {
        array_push($products, $_product);
    }
endwhile;
```

## Product Information

```
// Get Product ID
$product->get_id(); (fixes the error: 'Notice: id was called incorrectly. Product properties should not be accessed directly')
// Get Product General Info
$product->get_type();
$product->get_name();
$product->get_slug();
$product->get_date_created();
$product->get_date_modified();
$product->get_status();
$product->get_featured();
$product->get_catalog_visibility();
$product->get_description();
$product->get_short_description();
$product->get_sku();
$product->get_menu_order();
$product->get_virtual();
$product->get_meta();
get_permalink( $product->get_id() );
// Get Product Prices
$product->get_price();
$product->get_regular_price();
$product->get_sale_price();
$product->get_date_on_sale_from();
$product->get_date_on_sale_to();
$product->get_total_sales();
// Get Product Tax, Shipping & Stock
$product->get_tax_status();
$product->get_tax_class();
$product->get_manage_stock();
$product->get_stock_quantity();
$product->get_stock_status();
$product->get_backorders();
$product->get_sold_individually();
$product->get_purchase_note();
$product->get_shipping_class_id();
// Get Product Dimensions
$product->get_weight();
$product->get_length();
$product->get_width();
$product->get_height();
$product->get_dimensions();
// Get Linked Products
$product->get_upsell_ids();
$product->get_cross_sell_ids();
$product->get_parent_id();
// Get Product Variations
$product->get_attributes();
$product->get_default_attributes();
// Get Product Taxonomies
$product->get_categories();
$product->get_category_ids();
$product->get_tag_ids();
// Get Product Downloads
$product->get_downloads();
$product->get_download_expiry();
$product->get_downloadable();
$product->get_download_limit();
// Get Product Images
$product->get_image_id();
$product->get_image();
$product->get_gallery_image_ids();
// Get Product Reviews
$product->get_reviews_allowed();
$product->get_rating_counts();
$product->get_average_rating();
$product->get_review_count();
```
Source: [BusinessBloomer](https://businessbloomer.com/woocommerce-easily-get-product-info-title-sku-desc-product-object/)

## Adding a new Product Type
Source: https://webkul.com/blog/create-a-custom-product-type-in-woocommerce/
* Register a new product type.
* Add a corresponding tab to it when selected.
* Add fields to the created tab.
* And finally show those details on the front at product page.

So, let’s start by firstly registering a product type. In my case, I am creating a product type ‘demo’.
 
Below code snippets shows you how to register a new product type which executes at ‘init’ hook at the function file of the plugin.

### Register a new Product Type
```
add_action( 'init', 'register_demo_product_type' );
 
function register_demo_product_type() {
 
  class WC_Product_Demo extends WC_Product {
            
    public function __construct( $product ) {
        $this->product_type = 'demo';
    parent::__construct( $product );
    }
  }
}
```

In above code, we create a new product type as ‘demo’ by extending the Woocommerce WC_Product class.

Then, after registering the product type we need to add the product type to the product type selector for choosing what kind of product the user wants to make. For that we add our new product type with default product type provided by Woocommerce as shown in the code below:

```
add_filter( 'product_type_selector', 'add_demo_product_type' );
 
function add_demo_product_type( $types ){
    $types[ 'demo' ] = __( 'Demo product', 'dm_product' );
 
    return $types;    
}
```

### Adding a new Tab to the Product Type
After adding the option to select the new product type we need to add a new tab associated with it and  we will add a field ‘ Demo Product Spec ‘ for describing the product type. This can be done by

```
add_filter( 'woocommerce_product_data_tabs',
'demo_product_tab' );
 
function demo_product_tab( $tabs) {
    $tabs['demo'] = array(
      'label'     => __( 'Demo Product', 'dm_product' ),
      'target' => 'demo_product_options',
      'class'  => 'show_if_demo_product',
     );
    return $tabs;
}
```

### Adding a new Field to the Tab
And for adding  field to it we need to add the execute following code on ‘woocommerce_product_data_panels’ hook.

```
add_action( 'woocommerce_product_data_panels', 
'demo_product_tab_product_tab_content' );
 
function demo_product_tab_product_tab_content() {
 
 ?><div id='demo_product_options' class='panel woocommerce_options_panel'><?php
 ?><div class='options_group'><?php
                
    woocommerce_wp_text_input(
    array(
      'id' => 'demo_product_info',
      'label' => __( 'Demo Product Spec', 'dm_product' ),
      'placeholder' => '',
      'desc_tip' => 'true',
      'description' => __( 'Enter Demo product Info.', 'dm_product' ),
      'type' => 'text'
    )
    );
 ?></div>
 </div><?php
}
```

### Saving the Data
The last step to do in admin side process is to save the details of the field ‘Demo Product Spec’ .

To save those details we will use the hook ‘ woocommerce_process_product_meta’ and add save the data in the postmeta as shown in below code snippet :

```
add_action( 'woocommerce_process_product_meta', 'save_demo_product_settings' );
    
function save_demo_product_settings( $post_id ){
        
    $demo_product_info = $_POST['demo_product_info'];
        
    if( !empty( $demo_product_info ) ) {
 
    update_post_meta( $post_id, 'demo_product_info', esc_attr( $demo_product_info ) );
    }
}
```

### Saving the selected Product Type
Source: [Stackoverflow](https://stackoverflow.com/a/43584777/10871765)

```
function woocommerce_product_class( $classname, $product_type ) {
if ( $product_type == 'aussengeraet' )
        $classname = 'WC_Aussengeraet';
    } elseif ($product_type == 'klimaanlage') {
        $classname = 'WC_Klimaanlage';
    }
 
return $classname;
}
add_filter( 'woocommerce_product_class', 'woocommerce_product_class', 10, 2 );
```

### Showing the Data on the Page
At last, we need to show the information that we added to the demo product type at the product page.

For that we’ll execute the below code on  hook  ‘woocommerce_single_product_summary‘  and add the details to the product page in the front as :

```
add_action( 'woocommerce_single_product_summary', 'demo_product_front' );
    
function demo_product_front () {
    global $product;
 
    if ( 'demo' == $product->get_type() ) {      
       echo( get_post_meta( $product->get_id(), 'demo_product_info' )[0] );
 
  }
}
```

## Custom Product Fields

→ [View Adding a new Product Type](#adding-a-new-product-type)
In this post, I’m going to walk through the entire process of adding WooCommerce custom fields to a product. We’ll look at two types of custom fields:
 
1. Firstly, input fields (also called product add-ons) like text fields, select fields, checkboxes, and so on that allow the user to enter additional, personalised information about a product
2. Secondly, we’ll look at WooCommerce extra product data fields – custom fields that display additional information for your products.
 
We’ll also take a look at how you can use both types of custom field in combination, displaying extra product data depending on certain user choices.

### Custom Add-on Fields
Personalization of the Product by the end user, for more visit [https://pluginrepublic.com/add-custom-fields-woocommerce-product/#what-woocommerce-custom-fields](https://pluginrepublic.com/add-custom-fields-woocommerce-product/#what-woocommerce-custom-fields)

### Extra Product Data Fields

#### How to add WooCommerce custom product data fields (Programmatically)
##### Adding the field in the Backend
```
/**
 * Display the custom text field
 * @since 1.0.0
 */
function cfwc_create_custom_field() {
 $args = array(
 'id' => 'custom_text_field_title',
 'label' => __( 'Custom Text Field Title', 'cfwc' ),
 'class' => 'cfwc-custom-field',
 'desc_tip' => true,
 'description' => __( 'Enter the title of your custom text field.', 'ctwc' ),
 );
 woocommerce_wp_text_input( $args );
}
add_action(
'woocommerce_product_options_general_product_data',
'cfwc_create_custom_field' );
```
Source: [GitHub](https://gist.github.com/plugin-republic/3cf647790521550d379e108039e5fc2b#file-custom-fields-for-woocommerce-php)



##### Saving the Data
```
/**
 * Save the custom field
 * @since 1.0.0
 */
function cfwc_save_custom_field( $post_id ) {
 $product = wc_get_product( $post_id );
 $title = isset( $_POST['custom_text_field_title'] ) ? $_POST['custom_text_field_title'] : '';
 $product->update_meta_data( 'custom_text_field_title', sanitize_text_field( $title ) );
 $product->save();
}
add_action( 'woocommerce_process_product_meta',
'cfwc_save_custom_field' );
```

##### Getting the Data in the Product Page
```
 $title = $product->get_meta( 'custom_text_field_title' );
 ```
 
 ## Declaring WooCommerce Support
If you are using custom WooCommerce template overrides in your theme you need to declare WooCommerce support using the add_theme_support function. 

WooCommerce template overrides are only enabled on themes that declare WooCommerce support. If you do not declare WooCommerce support in your theme, WooCommerce will assume the theme is not designed for WooCommerce compatibility and will use shortcode-based unsupported theme rendering to display the shop.

Declaring WooCommerce support is straightforward and involves adding one function in your theme’s functions.php file.

### Basic Usage
```
function mytheme_add_woocommerce_support() {
add_theme_support( 'woocommerce' );
}
 
add_action( 'after_setup_theme',
'mytheme_add_woocommerce_support' );
```

Make sure you are using the `after_setup_theme` hook and not the init hook. Read more about this at the documentation for [add_theme_support](https://developer.wordpress.org/reference/functions/add_theme_support/).

### Usage with Settings
```
function mytheme_add_woocommerce_support() {
add_theme_support( 'woocommerce', array(
'thumbnail_image_width' => 150,
'single_image_width' => 300,
'product_grid' => array(
'default_rows' => 3,
'min_rows' => 2,
'max_rows' => 8,
'default_columns' => 4,
'min_columns' => 2,
'max_columns' => 5,
),
) );
}
 
add_action( 'after_setup_theme', 'mytheme_add_woocommerce_support' );
```

These are optional theme settings that you can set when declaring WooCommerce support.

`thumbnail_image_width` and `single_image_width` will set the image sizes for the shop. If these are not declared when adding theme support, the user can set image sizes in the Customizer under the WooCommerce > Product Images section.

The product_grid settings let theme developers set default, minimum, and maximum column and row settings for the Shop. Users can set the rows and columns in the Customizer under the WooCommerce > Product Catalog section.

### Product Gallery Features
The product gallery introduced in 3.0.0 ([read here for more information](https://woocommerce.wordpress.com/2016/10/19/new-product-gallery-merged-in-to-core-for-2-7/)) uses Flexslider, Photoswipe, and the jQuery Zoom plugin to offer swiping, lightboxes and other neat features.

In versions 3.0, 3.1, and 3.2, the new gallery is off by default and needs to be enabled using a snippet (below) or by using a compatible theme. This is because it’s common for themes to disable the WooCommerce gallery and replace it with their own scripts.

In versions 3.3+, the gallery is off by default for WooCommerce compatible themes unless they declare support for it (below). 3rd party themes with no WooCommerce support will have the gallery enabled by default.

To enable the gallery in your theme, you can declare support like this:

```
add_theme_support( 'wc-product-gallery-zoom' );
add_theme_support( 'wc-product-gallery-lightbox' );
add_theme_support( 'wc-product-gallery-slider' );
```
You do not have to support all 3 parts of the gallery; you can pick and choose features. If a feature is not enabled, the scripts will not be loaded and the gallery code will not execute on product pages.

If gallery features are enabled (e.g. you have a theme which enabled them, or you are running a non-WC compatible theme), you can disable them with remove_theme_support:
```
remove_theme_support( 'wc-product-gallery-zoom' );
remove_theme_support( 'wc-product-gallery-lightbox' );
remove_theme_support( 'wc-product-gallery-slider' );
```
You can disable any parts; you do not need to disable all features.


## Template Structure
WooCommerce template files contain the markup and template structure for frontend and HTML emails of your store. If some structural change in HTML is necessary, you should override a template.
When you open these files, you will notice they all contain hooks that allow you to add/move content without needing to edit template files themselves. This method protects against upgrade issues, as the template files can be left completely untouched.
Template files can be found within the */woocommerce/templates/* directory.

### How to Edit Files
Edit files in an upgrade-safe way using overrides. Copy it into a directory within your theme named /woocommerce keeping the same file structure but removing the /templates/subdirectory.

Example: To override the admin order notification, copy: 
`wp-content/plugins/woocommerce/templates/emails/admin-new-order.php`
 to
`wp-content/themes/yourtheme/woocommerce/emails/admin-new-order.php`
The copied file will now override the WooCommerce default template file.

Warning: Do not delete any of WooCommerce hooks when overriding a template. This would prevent plugins hooking in to add content.
 
Warning: Do not edit these files within the core plugin itself as they are overwritten during the upgrade process and any customizations will be lost.

## CSS Structure
Inside the assets/css/ directory, you will find the stylesheets responsible for the default WooCommerce layout styles.

Files to look for are woocommerce.scss and woocommerce.css.
woocommerce.css is the minified stylesheet – it’s the CSS without any of the spaces, indents, etc. This makes the file very fast to load. This file is referenced by the plugin and declares all WooCommerce styles.

woocommerce.scss is not directly used by the plugin, but by the team developing WooCommerce. We use SASS in this file to script the CSS in the first file easier and faster.

The CSS is written to make the default layout compatible with as many themes as possible by using % widths for all layout styles. It is, however, likely that you’ll want to make your own adjustments.

### Modifications
To avoid upgrade issues, we advise not editing these files but rather use them as a point of reference.

If you just want to make changes, we recommend adding some overriding styles to your theme stylesheet. For 
example, add the following to your theme stylesheet to make WooCommerce buttons black instead of the default color:
```
a.button,
button.button,
input.button,
#review_form #submit {
	background:black;
}
```
WooCommerce also outputs the theme name (plus other useful information, such as which type of page is being viewed) as a class on the body tag, which can be useful for overriding styles.

### Disabling WooCommerce Styles
If you plan to make major changes, or create a theme from scratch, then you may prefer your theme not reference the WooCommerce stylesheet at all. You can tell WooCommerce to not use the default woocommerce.css.

Add the following code to your theme’s functions.php file:
`add_filter( 'woocommerce_enqueue_styles', '__return_false' );`

With this definition in place, your theme will no longer use the WooCommerce stylesheet and give you a blank canvas upon which you can build your own desired layout/style.

Styling a WooCommerce theme from scratch for the first time is no easy task. There are many different pages and elements that need to be styled, and if you’re new to WooCommerce, you are probably not familiar with many of them. A non-exhaustive list of WooCommerce elements to style can be found here.

## Examples
[Storefront](https://wordpress.org/themes/storefront/) is the official WooCommerce theme built to the same exact standards as WooCommerce itself. The code is available on [GitHub](http://github.com/woocommerce/storefront).
If you would like to see examples of other themes that have added WooCommerce support and with their own styles, we suggest looking at the following themes, which can be downloaded from their showcase pages on [WordPress.com](http://wordpress.com/):
* [Karuna](https://wordpress.com/theme/karuna)
* [Shoreditch](https://wordpress.com/theme/shoreditch)
* [Independent Publisher 2](https://wordpress.com/theme/independent-publisher-2)
* [Pique](https://wordpress.com/theme/pique)
* [Libre 2](https://wordpress.com/theme/libre-2)
* [Button 2](https://wordpress.com/theme/button-2)
* [Radcliffe 2](https://wordpress.com/theme/radcliffe-2)
* [Lodestar](https://wordpress.com/theme/lodestar)
* [Product Custom Data](https://pluginrepublic.dev/product-extras/product/build-your-own-computer/)

## Sources
* https://businessbloomer.com/woocommerce-easily-get-product-info-title-sku-desc-product-object/
* https://stackoverflow.com/questions/53356016/get-and-display-related-related-products-in-woocommerce#answer-53356356
* https://pluginrepublic.com/add-custom-fields-woocommerce-product/
* https://webkul.com/blog/create-a-custom-product-type-in-woocommerce/

## Relevant Links
* [Image Sizes for Theme Developers](https://docs.woocommerce.com/document/image-sizes-theme-developers/)
* [Conditional Tags](https://docs.woocommerce.com/document/conditional-tags/)
* [Display Category Image on Category Archive](https://docs.woocommerce.com/document/woocommerce-display-category-image-on-category-archive/)
* [Fixing Outdated WooCommerce Templates](https://docs.woocommerce.com/document/fix-outdated-templates-woocommerce/)
* [Edit Theme CSS with Firebug](https://support.woothemes.com/hc/en-us/articles/203105957-Customizing-your-theme-with-Firebug)
* [How to Get Help](https://docs.woocommerce.com/document/how-to-get-help/)



