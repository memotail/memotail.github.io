---
layout:     post
title:      "cordova-plugin-contacts 实现查询以及保存到手机通讯录"
subtitle:   "走在APP的路上"
date:       2016-11-30
author:     "Memotail"
// header-img: "img/post-bg-ios9-web.jpg"
catalog:    true
tags:
    - Cordova
    - Contacts
---

在使用cordova进行hybrid App开发中，若需求需要操作手机通讯录时，首选使用cordova-plugin-contacts，通过该插件，能实现手机通讯录的增删改查。

## 对象

##### Contact                              联系人对象


##### ContactName   联系人名称对象

  **Parameters**

  - formatted   名称格式化文本

  - familyName  性别

  - givenName   名称

  - middleName  名称中间

  - honorificPrefix 前缀

  - honorificSuffix 后缀

  
##### ContactField  联系人字段对象
  
  **Parameters**
  
  - type 类型
  
  - value 值

  - pref  是否优先

  
  > type: work(工作)，mobile(手机），home(家庭)，等


##### ContactAddress    联系人地址对象

  **Parameters**

  - pref  是否优先

  - type  类型

  - formatted 格式化文本

  - streetAddress 街道

  - locality  市

  - region    省

  - postalCode    邮政编码

  - country   国家


##### ContactOrganization   联系人组织对象

  **Parameters**

  - pref  是否优先

  - type  类型

  - name  公司

  - department    部门

  - title 头衔


##### ContactFindOptions    contacts.find函数最后一个参数，用于过滤
    
  **Parameters**

  - filter    过滤字段

  - multiple  是否查询多个

  - desiredFields 获取的字段

  - hasPhoneNumber    是否存在手机号码
  
  > desiredFields中，若定义字段，则获取的列表，只有这些字段有值


## 常量

##### ContactFieldType
    navigator.contacts.fieldType, 如 navigator.contacts.fieldType.addresses

| 名称            | 类型                | 描述        |
| :-------------: | :--------------:    | :---------: |
| addresses       | ContactAddress[]    | 地址        |
| birthday        | Date                | 生日        |
| categories      | ContactField[]      | 分类        |
| country         | DOMString           | 国家        |
| department      | DOMString           | 部门        |
| displayName     | DOMString           | 可视名称    |
| emails          | ContactField[]      | 电子邮箱    |
| familyName      | DOMString           | 性别        |
| formatted       | DOMString           | 格式化全名  |
| givenName       | DOMString           | 名称        |
| honorificPrefix | DOMString           | 前缀        |
| honorificSuffix | DOMString           | 后缀        |
| id              | DOMString           | id          |
| ims             | ContactField[]      | im          |
| locality        | DOMString           | 市          |
| middleName      | DOMString           | 中间名称    |
| name            | ContactName         | 联系人名称  |
| nickname        | DOMString           | 联系人昵称  |
| note            | DOMString           | 备注        |
| organizations   | ContactOrganization | 组织        |
| phoneNumbers    | ContactField[]      | 电话号码    |
| photos          | ContactField[]      | 邮编        |
| postalCode      | DOMString           | 邮编        |
| region          | DOMString           | 省          |
| streetAddress   | DOMString           | 街道地址    |
| title           | DOMString           | 头衔        |
| urls            | ContactField[]      | 网址        |


##### ContactError  错误对象，函数报错后返回的对象
    
  - ContactError.UNKNOWN_ERROR (code 0)

  - ContactError.INVALID_ARGUMENT_ERROR (code 1)
    
  - ContactError.TIMEOUT_ERROR (code 2)
    
  - ContactError.PENDING_OPERATION_ERROR (code 3)
    
  - ContactError.IO_ERROR (code 4)
    
  - ContactError.NOT_SUPPORTED_ERROR (code 5)
    
  - ContactError.OPERATION_CANCELLED_ERROR (code 6)
    
  - ContactError.PERMISSION_DENIED_ERROR (code 20)

## 函数

##### navigator.contacts.find          获取手机通讯录列表

  **Parameters**

- fields    {Array[ContactFieldType]} 筛选配对的字段集合

- successCB {Function}  成功回调

- errorCB   {Function}  失败回调

- options   {ContactFindOptions}    删选条件，若不需要删选，则不用传递该对象


```js

// 从联系人全称、名称、手机号码中，获取所有关键字为’陈’的联系人
const fields = [
  navigator.contacts.fieldType.displayName, 
  navigator.contacts.fieldType.name, 
  navigator.contacts.fieldType.phoneNumbers
];

const options = new ContactFindOptions();
options.filter = '陈';   // 从fields定义字段中筛选
options.multiple = true;

navigator.contacts.find(fields, (result) => {
  console.log(result);
}, (ContactError) => {
  console.error(ContactError);
}, options);

```

> 若直接获取全部通讯录项，则不需要传递options进行过滤

> 在使用中，注意 android 使用displayName（android的name.formatted是英文的反向规则），而ios 使用name.formatted



###### navigator.contacts.create     创建一个联系人

  const contact = navigator.contacts.create();
  contact.displayName = 'memotail';
  contact.nickname = 'vivian';


  // 保存联系人到手机通讯录
  contact.save((item) => {
    console.log(item);
  }, (contactError) => {
    console.error(contactError);
  });


## 综合例子

```js

// 保存某个联系人到手机通讯录
createContact({
  name: {
    familyName: 'memotail'
  },
  displayName: 'memotail',
  phoneNumbers: [{
    type: 'mobile',
    value: '13000000000',
    pref: true
  }, {
    type: 'work',
    value: '0755-1234567',
    pref: false
  }],
  organizations: [{
    name: data.company_name,
    department: data.position
  }],
  note: 'hello world!'
});

// 字段的类型常量，用于构造内容使用
const fieldDataType = {
  id: 'DOMString',
  displayName: 'DOMString',
  name: 'ContactName',
  nickname: 'DOMString',
  phoneNumbers: 'ContactField', // [ContactField]
  emails: 'ContactField', // [ContactField]
  addresses: 'ContactAddress', // [ContactAddress]
  ims: 'ContactField', // [ContactField]
  organizations: 'ContactOrganization', // [ContactOrganization]
  birthday: 'Date',
  note: 'DOMString',
  photos: 'ContactField', // [ContactField]
  categories: 'ContactField', // [ContactField]
  urls: 'ContactField' // [ContactField]
};

/**
 * 创建联系人保存到手机，若存在，则提示用户是否覆盖
 * @param  {Object} fields 保存的内容
 */
function create(fields) {
  // 新的联系人，获取电话号码
  const newPrimaryPhone = _getPrimaryPhone(fields.phoneNumbers);

  if (newPrimaryPhone) {
    // findFields ，options 同上，略
    navigator.contacts.find(findFields, (contact) => {
      // 若存在相同手机号，则提示
      const deleteArray = [];

      contact.forEach((item) => {
        if (item.phoneNumbers) {
          // 获取已存在的电话号码
          const primaryPhone = _getPrimaryPhone(item.phoneNumbers);

          // 手机完全相同，则调用删除，去掉重复项
          if (primaryPhone && primaryPhone.value === newPrimaryPhone.value) {
            deleteArray.push(item);
          }
        }
      });

      if (deleteArray && deleteArray.length) {
        const result = confirm('存在相同联系人是否覆盖 ? ');
        if (result) {
          deleteArray.forEach((contact) => {
            removeContact(contact); // 删除相同手机号的联系人
          });
        }
      }
      createContact(fields); // 保存联系人到手机通讯录
    }, (ContactError) => {
      console.error(ContactError);
    }, options);
  } else {
    createContact(fields); // 保存联系人到手机通讯录
  }

}

/**
 * 创建联系人保存到手机
 * @param  {Object} fields 保存的内容
 */
function createContact(fields) {
  const contact = navigator.contacts.create();

  // 循环内容，构造联系人对象
  for (const e in fields) {
    if (fields.hasOwnProperty(e)) {
      const field = fields[e];
      const dataType = fieldDataType[e];
      contact[e] = _buildFieldByType(dataType, field);
    }
  }

  // 保存联系人到手机通讯录
  contact.save((item) => {
    console.log(item);
  }, (contactError) => {
    console.error(contactError);
  });
}

// 拼接类型对象
function _buildFieldByType(type, field) {
  // 数组需处理
  if (Object.prototype.toString.call(field) === '[object Array]') {
    return field.map((item) => {
      return _buildFieldByType(type, item);
    });
  }

  let filedObject;

  switch (type) {
    case 'ContactField': // 类型字段
      filedObject = new ContactField();
      break;
    case 'ContactName': // 名称
      filedObject = new ContactName();
      break;
    case 'ContactAddress': // 地址
      filedObject = new ContactAddress();
      break;
    case 'ContactOrganization': // 组织
      filedObject = new ContactOrganization();
      break;
    default:
      return field; // 字符串
  }

  // 组装字段
  for (const e in field) {
    if (field.hasOwnProperty(e)) {
      filedObject[e] = field[e];
    }
  }

  return filedObject;
}

/**
 * 获取首个mobile类型的号码
 * @param  {Array} phoneNumbers 联系人对象的号码集合
 * @return {Object}              返回第一个mobile
 */
_getPrimaryPhone(phoneNumbers) {
  let result = null;
  result = phoneNumbers.find((phone) => {
    // 手机完全相同，则调用删除
    return phone.type === 'mobile';
  });

  return result;
}

/**
 * 删除某个联系人
 * @param  {Object} contactItem 联系人对象
 */
function removeContact(contact) {
  contact.remove(() => {
    console.log('success');
  }, (contactError) => {
    console.error(contactError);
  });
}
```