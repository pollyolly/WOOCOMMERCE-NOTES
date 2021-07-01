## WOOCOMMERCE-NOTES
### ADD CUSTOMFIELD IN CHECKOUT
```
add_action('woocommerce_before_checkout_form', 'customise_checkout_field');
 
function customise_checkout_field($checkout)
{
    echo '<div id="customise_checkout_field"><h2>' . __('') . '</h2>';
    woocommerce_form_field('giftoption', array(
        'type' => 'textarea',
        'class' => array(
            'my-field-class form-row-wide'
        ) ,
        'label' => __('Gift message') ,
        'placeholder' => __('Please enter gift message..') ,
        'required' => false,
    ) , $checkout->get_value('giftoption'));
    echo '</div>';
}
```
### ADD CUSTOM DATA ORDER BILLING
```
add_action( 'woocommerce_admin_order_data_after_billing_address', 'my_custom_checkout_field_display_admin_order_meta', 10, 1 );
function my_custom_checkout_field_display_admin_order_meta($order){
       echo "Your Custom Text: Therichpost";
}
```
### PDF DOWNLOAD LINK IN SINGLE PRODUCT PAGE
```
add_action('woocommerce_before_single_product_summary','download_pdf',11);
function download_pdf()
{
    echo "<a href='add_your_pdf_link' target='_blank'>Download PDF</a>"; 
}
```
### CHECKOUT PAGE NOTICE
```
add_action('woocommerce_before_checkout_form', 'my_custom_message');
function my_custom_message() {
        wc_print_notice( __('Enter your custom message'), 'notice' );
}
```
### MAKE PRODUCT OUT OF STOCK AFTER ORDER
```
add_action( 'woocommerce_thankyou', 'change_product_status', 10, 1 );
function change_product_status( $order_id ) {
if ( ! $order_id )
return;
$order = wc_get_order( $order_id );
foreach ($order->get_items() as $item_id => $item_values) {
$product_id = $item_values['product_id'];
update_post_meta($product_id, '_stock', 0);
wc_update_product_stock_status($product_id, "outofstock");
}
}
```
