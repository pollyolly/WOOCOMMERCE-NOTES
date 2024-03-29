## WOOCOMMERCE-NOTES
```
Reference: https://therichpost.com/
```
### ADD CUSTOMFIELD IN CHECKOUT
```php
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
### ADD CUSTOMFIELD IN CHECKOUT AND VALIDATE
```php
/////#Add New Field#/////
add_action( 'woocommerce_after_checkout_billing_form', 'add_extra_fields_after_billing_address',1,1)
function add_extra_fields_after_billing_address() {
  _e( "Referral:", "add_extra_fields");
  ?>
  <br>
  <select name="add_referral" class="add_referral">
      <option value="">Select Referral</option>
    <option value="Google">Google</option>
    <option value="Facebook">Facebook</option>
    <option value="Other company">Other company</option>
  </select>
  <script>
    jQuery(document).ready(function($){
      $(document).ready(function() {
        $('.add_referral').select2();
      });
    })
  </script>
  <?php 
}
/////#Add New Field Validation#/////
add_action('woocommerce_after_checkout_validation', 'add_validate',10,2);
function add_validate($data,$errors) { 
  if ( isset( $_POST['add_referral'])  && empty( $_POST['add_referral'])){
    $errors->add( 'validation', __( "<strong>Referral</strong> is a required field." ));
  }
}
```
### ADD CUSTOM DATA ORDER BILLING
```php
add_action( 'woocommerce_admin_order_data_after_billing_address', 'my_custom_checkout_field_display_admin_order_meta', 10, 1 );
function my_custom_checkout_field_display_admin_order_meta($order){
       echo "Your Custom Text: Therichpost";
}
```
### PDF DOWNLOAD LINK IN SINGLE PRODUCT PAGE
```php
add_action('woocommerce_before_single_product_summary','download_pdf',11);
function download_pdf()
{
    echo "<a href='add_your_pdf_link' target='_blank'>Download PDF</a>"; 
}
```
### CHECKOUT PAGE NOTICE
```php
add_action('woocommerce_before_checkout_form', 'my_custom_message');
function my_custom_message() {
        wc_print_notice( __('Enter your custom message'), 'notice' );
}
```
### MAKE PRODUCT OUT OF STOCK AFTER ORDER
```php
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
### CUSTOM  SEARCH
```php
<form method="get" id="productS">
<input type="text" name="searchtxt" class="" />
<button type="search"><i class="icon-search searcBtn"></i></button>
</form>
<?php 
/*Search*/
  if(isset($_GET['searchtxt']))
  {
    global $wpdb;
    $result = $wpdb->get_results("SELECT * FROM `wp_posts` WHERE `post_title` like '%".$_GET['searchtxt']
    ."%' and post_type = 'product'");
    foreach ($result as $product) {
      $_product = wc_get_product( $product->ID );
      $attachment_ids[0] = get_post_thumbnail_id($product->ID);
      $attachment = wp_get_attachment_image_src($attachment_ids[0], 'thumbnail' );
      ?>
      <ul>
      <li><img src="<?php echo $attachment[0] ?>" /></li>
      <li><?php echo $product->post_title ?></li>  
      </ul>
      <?php
    }
  } 
/*Search*/
?>
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<script type="text/javascript">
  jQuery(document).ready(function($) {
    $(".searcBtn").click(function(event) {
      $("#productS").submit();
    });
  });
</script>
```
### SHOW PRODUCTS IN FRONTEND
```php
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css">
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js"></script>
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.1.3/js/bootstrap.min.js"></script>
  <div class="container">
  <table class="table table-hover">
    <thead class="thead-dark">
      <tr>
        <th>Product Name</th>
        <th>Product Price</th>
        <th>Product Type</th>
        <th>Status</th>
        <th>Added By</th>
      </tr>
    </thead>
    <tbody>
<?php
    $args = array(
        'post_type' => 'product',
        'posts_per_page' => -1
    );
    $loop = new WP_Query( $args );
    if ( $loop->have_posts() ): while ( $loop->have_posts() ): $loop->the_post();
        global $product
    ?>
     <tr>
        <td><?php echo '<a href="'. get_permalink() .'"><img style="width:40px;height:40px;margin-right:5px;" class="img-responsive" src="' . get_the_post_thumbnail_url() . '">'.get_the_title().'</a>'; ?></td>
        <td><?php echo get_post_meta( get_the_ID(), '_price', true ); ?></td>
        <td><?php echo $product->product_type; ?></td>
        <td><?php echo get_post_meta( get_the_ID(), '_stock_status', true ); ?></td>
        <td><?php $author_id = get_post_field ('post_author', $cause_id);
    $display_name = get_the_author_meta( 'display_name' , $author_id ); 
    echo $display_name; ?></td>
      </tr>
    <?php
    endwhile; endif; wp_reset_postdata();
?>
</tbody>
</table>
</div>
```
### FILTER PRODUCT BASED ON CUSTOM FIELD
localhost/shop/?custom_text_field_delivery=24h
```php
add_filter( 'woocommerce_product_query_meta_query', 'filter_products_with_custom_field', 10, 2 );
function filter_products_with_custom_field( $meta_query, $query ) {
    $meta_key = 'custom_text_field_delivery'; // <= Here define the meta key
    
    if ( ! is_admin() && isset($_GET[$meta_key]) && ! empty($_GET[$meta_key]) ) {
        $meta_query[] = array(
           'key'   => $meta_key,
           'value' => esc_attr($_GET[$meta_key]),
        );
    }
    return $meta_query;
}
```
