# WordPress Plugin Security Guidelines

When generating or reviewing WordPress plugin code, you MUST adhere strictly to the following security practices. Treat every piece of user input as malicious.

## 1. Input Sanitization (Data Collection)
Before saving to the database or processing, ALWAYS sanitize user input. Never trust `$_POST`, `$_GET`, `$_REQUEST`, `$_COOKIE`, or `$_SERVER` data.
*   **Strings:** Use `sanitize_text_field()` or `sanitize_textarea_field()`.
*   **Emails:** Use `sanitize_email()`.
*   **File Names:** Use `sanitize_file_name()`.
*   **HTML:** Use `wp_kses()` or `wp_kses_post()` to allow only specific safe HTML tags.
*   **Keys/Classes:** Use `sanitize_key()` or `sanitize_html_class()`.
*   **Arrays:** Use `map_deep( $array, 'sanitize_text_field' )`.

## 2. Output Escaping (Data Display)
ALWAYS escape data immediately before echoing it to the browser. Late escaping is a strict requirement to prevent XSS (Cross-Site Scripting).
*   **HTML Text:** Use `esc_html()`.
*   **HTML Attributes:** Use `esc_attr()`.
*   **URLs:** Use `esc_url()` (or `esc_url_raw()` for database insertion/API requests).
*   **JavaScript:** Use `esc_js()`.
*   **Translation + Escaping:** Use `esc_html__()`, `esc_html_e()`, `esc_attr__()`, `esc_attr_e()`.

## 3. Database Security (SQL Injection)
Never pass raw variables directly into SQL queries.
*   **Prepared Statements:** ALWAYS use `$wpdb->prepare()` when making custom database queries.
*   **Placeholder Syntax:** Use `%s` for strings, `%d` for integers, and `%f` for floats.
*   **Example:** `$wpdb->query( $wpdb->prepare( "SELECT * FROM {$wpdb->prefix}table WHERE id = %d", $id ) );`
*   **Architecture:** Prefer built-in functions (e.g., `get_post()`, `get_user_meta()`) over direct `$wpdb` queries whenever possible.

## 4. Authorization (Permissions)
Verify that the current user has the right to perform the requested action.
*   **Capability Checks:** ALWAYS use `current_user_can( 'capability' )` before executing sensitive logic.
*   **Admin Pages:** Ensure `add_menu_page()` and `add_submenu_page()` specify the correct capability (e.g., `manage_options`).
*   **REST API:** ALWAYS implement the `permission_callback` in `register_rest_route()`.

## 5. Nonces (CSRF Protection)
Protect against Cross-Site Request Forgery on all state-changing operations (form submissions, AJAX requests, deletions).
*   **Creation:** Use `wp_nonce_field( 'action_name', 'nonce_name' )` in forms.
*   **Verification (Standard):** Use `wp_verify_nonce( $_POST['nonce_name'], 'action_name' )`.
*   **Verification (AJAX):** Use `check_ajax_referer( 'action_name', 'nonce_name' )`.

## 6. File System & Uploads
*   **Direct Access:** Block direct file access by adding `if ( ! defined( 'ABSPATH' ) ) exit;` at the top of every PHP file.
*   **Uploads:** Use `wp_handle_upload()` for handling file uploads. Validate MIME types strictly.

## 7. Error Handling
*   Never expose raw PHP errors or database errors to the frontend.
*   Use `WP_Error` objects to handle and return gracefully.
