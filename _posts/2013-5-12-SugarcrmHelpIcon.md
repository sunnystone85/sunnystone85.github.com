---
layout: post
category: sugarcrm
tagline: Help Icon
tags: [Help, Help icon, help js]
---
{% include JB/setup %}
## 配置Help图标的显示
### 显示配置
#### a. vardefs.php 中定义help
    $dictionary['ibm_RevenueLineItems']['fields']['revenue_amount']['help'] = 
    'tooltip(advanced_search,basic_search): LBL_REVENUE_AMOUNT_HELP';
    // tooltip()中定义哪些页面需要这个icon(edit,basic_search)表示在editView和basic_search页面要显示
    // LBL_REVENUE_AMOUNT_HELP对应语言文件中的定义，如果是SearchForm页面的需要在语言文件中定义
    // LBL_REVENUE_AMOUNT_HELP_BASIC_SEARCH 和 LBL_REVENUE_AMOUNT_HELP_ADVANCED_SEARCH

#### b. view.list.php 需要这个文件进行处理
##### 此文件中的listViewProcess()方法中
    echo '<script type="text/javascript" src="'.getJSPath('custom/include/javascript/Help.js').'"></script>'; 用来引入Help.js

##### processSearchForm方法
    IBMHelper::getClass('Help')->addSearchViewHelp($this->bean,$this->searchForm->displayView);
    // 用来生成加入显示内容的js

##### 添加类似如下的js内容，然后由help.js定义的对应函数执行。
    addSearchViewHelp('{$field}{$field_sub}','$text');