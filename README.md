# WordPress WP List Table

## Current Generators

 * Settings Menu page with Handlers
 * WP List Table
 * Form Generator (new/edit)


################# ------WP List Table----####################

 
## wp-list-core

    <?php
    
    if ( ! class_exists ( 'WP_List_Table' ) ) {
        require_once ABSPATH . 'wp-admin/includes/class-wp-list-table.php';
    }
    
    /**
     * List table class
     */
    class Table_class extends WP_List_Table {
    
        function __construct() {
            parent::__construct( array(
                'singular' => 'book',
                'plural'   => 'books',
                'ajax'     => false
            ) );
        }
    
        function get_table_classes() {
            return array( 'widefat', 'fixed', 'striped', $this->_args['plural'] );
        }
    
        /**
         * Message to show if no designation found
         *
         * @return void
         */
        function no_items() {
            _e( 'No book found', 'textdomain' );
        }
    
        /**
         * Default column values if no callback found
         *
         * @param  object  $item
         * @param  string  $column_name
         *
         * @return string
         */
        function column_default( $item, $column_name ) {
    
            switch ( $column_name ) {
                case 'book_title':
                    return $item->book_title;
    
                case 'book_dic':
                    return $item->book_dic;
    
                default:
                    return isset( $item->$column_name ) ? $item->$column_name : '';
            }
        }
    
        /**
         * Get the column names
         *
         * @return array
         */
        function get_columns() {
            $columns = array(
                'cb'           => '<input type="checkbox" />',
                'book_title'      => __( 'Book Title', 'textdomain' ),
                'book_dic'      => __( 'Book Dic', 'textdomain' ),
    
            );
    
            return $columns;
        }
    
        /**
         * Render the designation name column
         *
         * @param  object  $item
         *
         * @return string
         */
        function column_book_title( $item ) {
    
            $actions           = array();
            $actions['edit']   = sprintf( '<a href="%s" data-id="%d" title="%s">%s</a>', admin_url( 'admin.php?page=test-page&action=edit&id=' . $item->id ), $item->id, __( 'Edit this item', 'textdomain' ), __( 'Edit', 'textdomain' ) );
            $actions['delete'] = sprintf( '<a href="%s" class="submitdelete" data-id="%d" title="%s">%s</a>', admin_url( 'admin.php?page=test-page&action=delete&id=' . $item->id ), $item->id, __( 'Delete this item', 'textdomain' ), __( 'Delete', 'textdomain' ) );
    
            return sprintf( '<a href="%1$s"><strong>%2$s</strong></a> %3$s', admin_url( 'admin.php?page=test-page&action=view&id=' . $item->id ), $item->book_title, $this->row_actions( $actions ) );
        }
    
        /**
         * Get sortable columns
         *
         * @return array
         */
        function get_sortable_columns() {
            $sortable_columns = array(
                'name' => array( 'name', true ),
            );
    
            return $sortable_columns;
        }
    
        /**
         * Set the bulk actions
         *
         * @return array
         */
        function get_bulk_actions() {
            $actions = array(
                'trash'  => __( 'Move to Trash', 'textdomain' ),
            );
            return $actions;
        }
    
        /**
         * Render the checkbox column
         *
         * @param  object  $item
         *
         * @return string
         */
        function column_cb( $item ) {
            return sprintf(
                '<input type="checkbox" name="book_id[]" value="%d" />', $item->id
            );
        }
    
        /**
         * Set the views
         *
         * @return array
         */
        public function get_views_() {
            $status_links   = array();
            $base_link      = admin_url( 'admin.php?page=sample-page' );
    
            foreach ($this->counts as $key => $value) {
                $class = ( $key == $this->page_status ) ? 'current' : 'status-' . $key;
                $status_links[ $key ] = sprintf( '<a href="%s" class="%s">%s <span class="count">(%s)</span></a>', add_query_arg( array( 'status' => $key ), $base_link ), $class, $value['label'], $value['count'] );
            }
    
            return $status_links;
        }
    
        /**
         * Prepare the class items
         *
         * @return void
         */
        function prepare_items() {
    
            $columns               = $this->get_columns();
            $hidden                = array( );
            $sortable              = $this->get_sortable_columns();
            $this->_column_headers = array( $columns, $hidden, $sortable );
    
            $per_page              = 15;
            $current_page          = $this->get_pagenum();
            $offset                = ( $current_page -1 ) * $per_page;
            $this->page_status     = isset( $_GET['status'] ) ? sanitize_text_field( $_GET['status'] ) : '2';
    
            // only ncessary because we have sample data
            $args = array(
                'offset' => $offset,
                'number' => $per_page,
            );
    
            if ( isset( $_REQUEST['orderby'] ) && isset( $_REQUEST['order'] ) ) {
                $args['orderby'] = $_REQUEST['orderby'];
                $args['order']   = $_REQUEST['order'] ;
            }
    
            $this->items  = wp_get_all_book( $args );
    
            $this->set_pagination_args( array(
                'total_items' => wp_get_book_count(),
                'per_page'    => $per_page
            ) );
        }
    }
    ?>
    
    
    
    
    //////////////////////////////--------------function-------------//////////
    
## Function getvaluees    
    
    
    

    <?php /**
     * Get all book
     *
     * @param $args array
     *
     * @return array
     */  function wp_get_all_book( $args = array()  )  {  global $wpdb; $defaults = array(  'number'  =>  20,  'offset'  =>  0,  'orderby'  =>  'id',  'order'  =>  'ASC',  ); $args = wp_parse_args( $args, $defaults ); $cache_key =  'book-all'; $items = wp_cache_get( $cache_key,  'textdomain'  );  if  (  false  === $items )  { $items = $wpdb->get_results(  'SELECT * FROM '  . $wpdb->prefix .  'book_db ORDER BY '  . $args['orderby']  .' '  . $args['order']  .' LIMIT '  . $args['offset']  .  ', '  . $args['number']  ); wp_cache_set( $cache_key, $items,  'textdomain'  );  }  return $items;  }  /**
     * Fetch all book from database
     *
     * @return array
     */  function wp_get_book_count()  {  global $wpdb;  return  (int) $wpdb->get_var(  'SELECT COUNT(*) FROM '  . $wpdb->prefix .  'book_db'  );  }  /**
     * Fetch a single book from database
     *
     * @param int   $id
     *
     * @return array
     */  function wp_get_book( $id =  0  )  {  global $wpdb;  return $wpdb->get_row( $wpdb->prepare(  'SELECT * FROM '  . $wpdb->prefix .  'book_db WHERE id = %d', $id )  );  }
     
     
     
     
     
     
     
     ////////////////////////////----------------------html---------------------//////////////////////
 ## HTML tabel view     
     
     
     
     
     <div class="wrap">
        <h2><?php _e(  'The Book',  'textdomain'  );  ?> <a href="<?php echo admin_url(  'admin.php?page=test-page&action=new'  );  ?>" class="add-new-h2"><?php _e(  'Add New',  'textdomain'  );  ?></a></h2>
        <form method="post">
            <input type="hidden" name="page" value="ttest_list_table">
            <?php
                $list_table =  new  Table_class(); $list_table->prepare_items(); $list_table->search_box(  'search',  'search_id'  ); $list_table->display();  ?>
        </form>
    </div>

