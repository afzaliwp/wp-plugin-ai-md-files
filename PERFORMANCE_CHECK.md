# WordPress Plugin Performance Guidelines

When generating or reviewing WordPress plugin code, you MUST prioritize efficiency, low memory footprint, fast execution times, and clean architecture. Adhere to the following rules to prevent bottlenecks.

## 1. Database Query Optimization
Database queries are the most common performance bottleneck.
*   **Avoid Custom Queries:** Use `WP_Query`, `get_posts()`, or `get_users()` instead of raw `$wpdb` queries when possible, as WP functions utilize caching.
*   **Limit Results:** Always set `posts_per_page` or `LIMIT` in SQL. NEVER query all records unconditionally.
*   **Return Only Necessary Fields:** If you only need post IDs, use `'fields' => 'ids'` in `WP_Query`.
*   **Meta Queries:** Avoid complex `meta_query` or `tax_query` arrays if possible, as they create expensive `JOIN` operations.
*   **No Wildcard Prefix:** Never use a leading wildcard in `LIKE` queries (e.g., `LIKE '%term'`) as it forces a full table scan.

## 2. Caching Strategy
Do not calculate expensive data or make identical external API calls multiple times.
*   **Transients:** Use Transients API (`set_transient()`, `get_transient()`, `delete_transient()`) for expensive database queries or external API responses that change infrequently.
*   **Object Cache:** Use `wp_cache_set()`, `wp_cache_get()`, and `wp_cache_delete()` for data needed multiple times within a single page load or across requests if persistent object caching (like Redis/Memcached) is enabled.

## 3. Options API Management
*   **Autoloading:** By default, `add_option()` and `update_option()` set the `autoload` flag to `yes`, meaning the data loads on *every* page load. 
*   **Rule:** If an option holds a large array of data, or is only used on a specific admin page, you MUST set `autoload` to `no`. 
    *   *Example:* `add_option( 'my_large_data', $data, '', 'no' );`

## 4. Asset Loading (CSS / JS)
*   **Conditional Enqueueing:** NEVER load plugin CSS/JS globally unless strictly necessary. ALWAYS check the current page hook or post type before calling `wp_enqueue_script()` or `wp_enqueue_style()`.
*   **Footers:** Load JavaScript in the footer by passing `true` to the `$in_footer` argument of `wp_enqueue_script()`.
*   **Minification:** Assume assets should be minified in production.

## 5. Background Processing
*   **Cron Jobs:** Do not run heavy tasks (e.g., syncing thousands of records, sending bulk emails) synchronously during a page load. Use `wp_schedule_event()` or Action Schedulers.
*   **AJAX:** For user-facing actions that take time, process them via AJAX so the user interface is not blocked.

## 6. Loops and Memory Management
*   **Unset Variables:** Free up memory by unsetting large arrays or objects when they are no longer needed (`unset( $large_array );`).
*   **WP_Query Cleanup:** ALWAYS call `wp_reset_postdata()` after a custom `WP_Query` loop to restore the global `$post` object.
*   **Batching:** If processing thousands of items, do it in batches (e.g., 100 at a time via AJAX or Cron) to avoid PHP timeout and memory exhaustion limits.

## 7. Code Architecture & Efficiency
*   **DRY Principle (Don't Repeat Yourself):** NEVER write duplicate code. If identical or highly similar logic appears in multiple places, extract it into a single, reusable helper function or class method. Centralized logic reduces memory overhead and makes optimization easier.
*   **Micro-Methods (Keep Functions Small):** Keep methods and functions as small as possible. Each function must have a Single Responsibility (do exactly one thing). Small functions are processed more efficiently by PHP's OPcache, utilize less memory during execution, and prevent unnecessary variable initialization.
