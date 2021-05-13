### 1. 简介

Auth权限管理比RBAC更加灵活，auth对权限的控制更为精细。

Auth的特性：

- 是对规则进行认证，不是对节点进行认证。用户可以把节点当作规则名称实现对节点进行认证。auth=newAuth();auth=newAuth();auth->check(‘规则名称’,’用户id’)
- 可以同时对多条规则进行认证，并设置多条规则的关系（or或者and）。auth=newAuth();auth=newAuth();auth->check(‘规则1,规则2’,’用户id’,’and’)
- 一个用户可以属于多个用户组（think_auth_group_access表 定义了用户所属用户组），我们需要设置每个用户组拥有哪些规则（think_auth_group 定义了用户组的权限）。
- 支持规则表达式。在think_auth_rule 表中定义一条规则时，如果type为1， condition字段就可以定义规则表达式。 如定义{score}>50 and {score}<100 表示用户的分数在50-100之间时这条规则才会通过。

### 2. 基本用法

四张相关数据表

```mysql
# 用户表
CREATE TABLE IF NOT EXISTS `think_user` (
  `uid` mediumint(8) unsigned NOT NULL AUTO_INCREMENT COMMENT '用户id',
  `username` varchar(20) NOT NULL DEFAULT '' COMMENT '用户名',
  `password` char(32) NOT NULL DEFAULT '' COMMENT '密码',
  `status` tinyint(1) unsigned NOT NULL DEFAULT 1 COMMENT '状态：0禁用，1正常',
  `score` mediumint(8) unsigned NOT NULL DEFAULT 0 COMMENT '积分',
  `login_time` int(10) unsigned NOT NULL DEFAULT 0 COMMENT '最近一次登录时间',
  `login_ip` int(11) NOT NULL DEFAULT '0' COMMENT '最近一次登录ip',
  PRIMARY KEY (`uid`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8 COMMENT='用户表';

# 用户-用户组关系表
CREATE TABLE IF NOT EXISTS `think_auth_group_access` (  
    `uid` mediumint(8) unsigned NOT NULL DEFAULT 0 COMMENT '用户id',  
    `group_id` mediumint(8) unsigned NOT NULL DEFAULT 0 COMMENT '组id', 
    UNIQUE KEY `uid_group_id` (`uid`,`group_id`),  
    KEY `uid` (`uid`), 
    KEY `group_id` (`group_id`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8 COMMENT='用户-用户组关系表';

# 规则表
CREATE TABLE IF NOT EXISTS `think_auth_rule` (  
    `id` mediumint(8) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键',  
    `name` char(80) NOT NULL DEFAULT '' COMMENT '规则的唯一标识（英文）',  
    `title` char(20) NOT NULL DEFAULT '' COMMENT '规则的中文名称',  
    `type` tinyint(1) NOT NULL DEFAULT '1',    
    `status` tinyint(1) NOT NULL DEFAULT '1' COMMENT '状态：1正常，0禁用',  
    `condition` char(100) NOT NULL DEFAULT '' COMMENT '规则的附加条件',  
    PRIMARY KEY (`id`),  
    UNIQUE KEY `name` (`name`)
) ENGINE=MyISAM  DEFAULT CHARSET=utf8 COMMENT='规则表';

# 用户组表
CREATE TABLE IF NOT EXISTS `think_auth_group` ( 
    `id` mediumint(8) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键', 
    `title` char(100) NOT NULL DEFAULT '' COMMENT '用户组的中文名称', 
    `status` tinyint(1) NOT NULL DEFAULT '1' COMMENT '状态：0禁用，1正常', 
    `rules` char(80) NOT NULL DEFAULT '' COMMENT '用户组拥有的规则id， 多个规则用,隔开', 
    PRIMARY KEY (`id`)
) ENGINE=MyISAM  DEFAULT CHARSET=utf8 COMMENT '用户组表';
```

