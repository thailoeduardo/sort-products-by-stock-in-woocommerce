# Sort products by stock in WooCommerce

The code snippet below is to be pasted in your theme's functions.php file implement the sorting out of stock WooCommerce products. Note that if you are using a child theme this code should be put in your child theme's functions.php file and not the parents theme's functions.php file.

```sh
add_filter('posts_clauses', function($posts_clauses){
  global $wpdb;
  // only change query on WooCommerce loops
  if (is_woocommerce() && (is_shop() || is_product_category() || is_product_tag() || is_archive()) || is_search() ) {
    $posts_clauses['join']		.= " LEFT JOIN (
                      SELECT pm.meta_value, p.ID FROM {$wpdb->posts} p
                      INNER JOIN {$wpdb->postmeta} pm ON p.ID = pm.post_id
                      WHERE pm.meta_key = '_stock_status' AND pm.meta_value = 'instock'
                      ORDER BY pm.meta_value ASC
                    ) as pm_stock ON ( {$wpdb->posts}.ID = pm_stock.ID) ";
    $posts_clauses['orderby']	= " pm_stock.meta_value DESC, " . $posts_clauses['orderby'];
  }

  return $posts_clauses;
}, 2000);
```
