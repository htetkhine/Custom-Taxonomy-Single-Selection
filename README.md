# Custom-Taxonomy-Single-Selection
This is very useful for functions that write single selections for custom taxonomies.
In WordPress, the selection type for custom taxonomies remains multiple select. For some projects, you may want to make custom taxonomies single select, which is very useful.

```php
<?php
function make_taxonomy_single_select($args, $taxonomy)
{
	if ($taxonomy === 'committee') {
		$args['meta_box_cb'] = 'single_select_taxonomy_meta_box';
	}
	return $args;
}
add_filter('register_taxonomy_args', 'make_taxonomy_single_select', 10, 2);


function single_select_taxonomy_meta_box($post, $box)
{
	$defaults = array('taxonomy' => 'category');
	if (!isset($box['args']) || !is_array($box['args'])) {
		$args = array();
	} else {
		$args = $box['args'];
	}
	$args = wp_parse_args($args, $defaults);

	$tax_name = esc_attr($args['taxonomy']);
	$taxonomy = get_taxonomy($tax_name);

	$terms = get_terms(array(
		'taxonomy' => $tax_name,
		'hide_empty' => false,
	));

	$name = 'tax_input[' . $tax_name . ']';
	$post_terms = get_the_terms($post->ID, $tax_name);
	$current = ($post_terms ? array_pop($post_terms) : false);
	$current = ($current ? $current->term_id : 0);
?>

	<div id="taxonomy-<?php echo $tax_name; ?>" class="categorydiv">
		<input type="hidden" name="<?php echo $name; ?>[]" value="0">
		<ul class="categorychecklist form-no-clear">
			<?php foreach ($terms as $term) : ?>
				<li id="<?php echo $taxonomy->name . '-' . $term->term_id; ?>">
					<label class="selectit">
						<input type="radio" name="<?php echo $name; ?>[]" value="<?php echo $term->term_id; ?>" <?php checked($current, $term->term_id); ?>>
						<?php echo esc_html($term->name); ?>
					</label>
				</li>
			<?php endforeach; ?>
		</ul>
	</div>
<?php
}
