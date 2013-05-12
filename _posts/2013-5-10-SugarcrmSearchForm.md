---
layout: post
category: sugarcrm
tagline: SearchForm 配置
tags: [sugarcrm, searchform, opitons, default_empty]
---
{% include JB/setup %}
## 页面显示配置
### 需要的文件(已ibm_RevenueLineItems为例)
#### a. modules/ibm_RevenueLineItems/vardefs.php 
定义所有的field，其中定义了所有对应的数据库表和字段:

    $dictionary['ibm_RevenueLineItems'] = array(
        'table'=>'ibm_revenuelineitems',
        'audited'=>true,
        'duplicate_merge'=>true,
        'fields'=>array (
            'revenue_amount' => 
                array (
                    'required' => true,
                    'name' => 'revenue_amount',
                    'vname' => 'LBL_REVENUE_AMOUNT',
                    'type' => 'currency',               
                    'massupdate' => 0,
                    'comments' => '',
                    'help' => '',
                    'importable' => 'true',
                    'duplicate_merge' => 'disabled',
                    'duplicate_merge_dom_value' => '0',
                    'audited' => false,
                    'reportable' => true,
                    'calculated' => false,
                    'len' => 26,
                    'size' => '20',
                    'enable_range_search' => false,            // 是否支持范围查找开关
                ),
#### b. modules/ibm_RevenueLineItems/metadata/searchdefs.php
定义页面要显示的元素以及布局，要跟vardefs.php中的field对应:

    $module_name = 'ibm_RevenueLineItems';
    $searchdefs[$module_name] = array(
        'templateMeta' => array(
            'maxColumns' => '3',
            'maxColumnsBasic' => '4', 
            'widths' => array('label' => '10', 'field' => '30'),
        ),
        'layout' => array(
            'basic_search' => array(
                'name',
                'revenue_amount' => array(
                    'name' => 'revenue_amount',
                    'type' => 'Amount',
                    'enable_range_search' => true,            
                    'options' => 'amount_range_search_dom',    // 定义下拉列表
                ),
##### 注 options 在对应的sugarField/*.tpl文件中
        {html_options options={{sugarvar key='options' string=true}} selected=$starting_choice}
        此处由sugarvar这个函数来取出定义的options值
##### 引申 options 使用的地方：custom/include/SearchForm/SearchForm2.php 通过设定default_empty可以定义下拉列表第一个值是否为空
        public function display($header = true) {
            if (!empty($this->fieldDefs)) {
                foreach ($this->fieldDefs as $field => $definition) {
                    // Add possibility of inserting empty value in front of enum field values on Search View
                    if (isset($definition['options'])
                            && is_array($definition['options'])
                            && isset($definition['default_empty'])
                            && !isset($definition['options'][$definition['default_empty']])) {
                        $this->fieldDefs[$field]['options'] = array(
                            $definition['default_empty'] => $definition['default_empty']
                                ) + $definition['options'];
                    }
                }
            }
            return $this->displayParent($header);
        }

## 功能配置
### 需要的文件
#### a. custom/modules/ibm_RevenueLineItems/metadata/SearchFields.php
    $searchFields[$module_name]['range_revenue_amount'] = array(
        'query_type' => 'default', 
        'enable_range_search' => true, 
        'db_field'=>array('CAST(round(IBM_REVENUELINEITEMS.REVENUE_AMOUNT/COALESCE(CURRENCIES.CONVERSION_RATE, 1), 6) as Decimal(26, 6))')
        );

#### b. custom/modules/ibm_RevenueLineItems/views/view.list.php
    class ibm_RevenueLineItemsViewList extends ViewListIBM
    {
        public function listViewProcess() {
        
            $this->params['custom_from'] = ' left join CURRENCIES on IBM_REVENUELINEITEMS.CURRENCY_ID = CURRENCIES.ID '; // SearchForm2.php 中会拼接这段
            
            $this->processSearchForm();
            $this->lv->searchColumns = $this->searchForm->searchColumns;

            if(!$this->headers)
                return;
            if(empty($_REQUEST['search_form_only']) || $_REQUEST['search_form_only'] == false){
                $this->lv->ss->assign("SEARCH",true);
                $this->lv->setup($this->seed, 'custom/modules/ibm_RevenueLineItems/tpls/ListViewGeneric.tpl', $this->where, $this->params); // 设定模板
                $savedSearchName = empty($_REQUEST['saved_search_select_name']) ? '' : (' - ' . $_REQUEST['saved_search_select_name']);
                echo $this->lv->display();
            }

            echo '<script type="text/javascript" src="'.getJSPath('custom/include/javascript/Help.js').'"></script>'; // help提示框的js
        }

        public function processSearchForm()
        {
            parent::processSearchForm();
            //Used to add tooltip help to search forms.
            IBMHelper::getClass('Help')->addSearchViewHelp($this->bean,$this->searchForm->displayView); // 根据配置生成help提示框的js
        }
    }

#### c. custom/include/SearchForm/SearchForm2.php 这个文件中 generateSearchWhere() 会拼接where语句。