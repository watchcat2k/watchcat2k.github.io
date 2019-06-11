---
layout: post
title: Django框架学习（四）：Django 单元测试
date: 2019-06-11 00:00:00
categories: 
- Django-web框架
tags: 
- Python
- Django
- 单元测试
description: Django 框架自带了单元测试工具，利用该工具，我们可以方便地对单元进行错误检查，以提高项目的质量。
---



Django 框架自带了单元测试工具，利用该工具，我们可以方便地对单元进行错误检查，以提高项目的质量。

# 编写单元测试
在 Django 框架中，当新建一个应用时，会默认新建一个用于单元测试的 `test.py` 文件，我们的单元测试代码就写在 `test.py` 里。

比如，在 `views.py` 里，我们写了一个相应用户登录操作的接口，如下：
```
@csrf_exempt
def user_password_session(request):
    """密码登录"""
    try:
        if request.method == 'POST':
            parameters = json.loads(request.body.decode('utf-8'))

            filter_user = models.User.objects.filter(user_phone=parameters['user_phone'])
            if filter_user.__len__() == 0:
                return HttpResponse(json.dumps(__notExistUser__), content_type='application/json', charset='utf-8')
            else:
                get_user = models.User.objects.get(user_phone=parameters['user_phone'])

                if parameters['user_password'] != get_user.user_password:
                    return HttpResponse(json.dumps(__wrongPassword__), content_type='application/json', charset='utf-8')
                else:
                    request.session['user_id'] = get_user.user_id
                    request.session['is_login'] = True

                    data = {
                        'user_fillln': get_user.user_fillln
                    }
                    __ok__['data'] = data

                    # 在cookie中设置csrftoken
                    get_token(request)

                    return HttpResponse(json.dumps(__ok__), content_type='application/json', charset='utf-8')
    except Exception as exc:
        print(exc)
        return HttpResponse(json.dumps(__error__), content_type='application/json', charset='utf-8')
```
这部分代码主要功能是：前端传手机号和密码的 json 数据到后端，后端通过搜索手机号来查找用户，若找不到对应用户，返回用户不存在的信息，否则若密码不正确，返回密码错误的信息，否则返回成功的信息。

对应的在 `tests.py` 单元测试代码为：
```
class UserTest(TestCase):
    '''测试用户API'''
    
    def setUp(self):
        '''测试函数执行前执行'''
        pass
        
    def test_post_user(self):
        '''创建用户'''
        pass   
    
	def test_post_user_password_session(self):
	        '''测试用户密码登录'''
	        self.maxDiff = None
	
	        new_user = models.User(
	            user_phone='15989061915',
	            user_password='123456'
	        )
	        new_user.save()
	
	        post_user_session_data = {
	            "user_phone": "1598906191",
	            "user_password": "123456"
	        }
	        response = self.client.post('/user/password/session', data=post_user_session_data, content_type='application/json')
	
	        # 验证该用户不存在
	        self.assertEqual(response.json(), __notExistUser__)
	
	        post_user_session_data = {
	            "user_phone": "15989061915",
	            "user_password": "12345"
	        }
	        response = self.client.post('/user/password/session', data=post_user_session_data, content_type='application/json')
	
	        # 验证密码错误
	        self.assertEqual(response.json(), __wrongPassword__)
	
	        post_user_session_data = {
	            "user_phone": "15989061915",
	            "user_password": "123456"
	        }
	        response = self.client.post('/user/password/session', data=post_user_session_data, content_type='application/json')
	
	        data = {
	            'user_fillln': 0
	        }
	        __ok__['data'] = data
	
	        # 验证OK
	        self.assertEqual(response.json(), __ok__)
	
	        # 验证此时确实是已登录状态
	        self.assertEqual(self.client.session['is_login'], True)

	def tearDown(self):
	        '''测试函数执行后执行'''
	        # 每个测试函数执行后都删除所有数据
	        models.User.objects.all().delete()
```
- 我们必须先创建一个继承 `TestCase` 的测试类 `UserTest` ，然后才能在类里面新建测试函数进行单元测试。
- 测试类里面有两个特殊函数 `setUp` 和 `tearDown` ，这两个函数是每个测试类里面的测试函数都会执行的函数，一个是测试函数执行前执行，另一个是测试函数执行后执行。
- 测试类里面的测试函数之间应该是独立互不影响的，比如 `test_post_user(self):` 创建用户和测试登录 `test_post_user_password_session(self):` 之间不应该有任何关联，比如数据上的关联，这样才符合单元测试中“单元”这两个字的理念。所以在测试登录里面，我是直接用 `models.User()` 新建一个用户，而不是通过调用创建用户的接口创建用户。并且每个测试函数执行后，在 `tearDown(self):` 里面，我都会删除所有的数据。
- 测试函数里通过 `response = self.client.post('/user/password/session', data=post_user_session_data, content_type='application/json')` 进行接口的请求，以测试对应接口是否正确。该函数的第一个参数是接口路径，第二个参数是请求的参数，第三个参数是请求的数据类型。通过 `response.json()` 即可获得返回的数据。
- 执行 `self.assertEqual(response.json(), __notExistUser__)` 即可验证用户不存在的情况，变换 `self.client.post():` 的请求参数和 `self.assertEqual():` 是第二个参数即可验证密码错误和成功这两个情况。
- 最后，执行 `python3 manage.py test` 即可执行单元测试，最终错误的个数和错误的地方将在命令行中显示出来。
