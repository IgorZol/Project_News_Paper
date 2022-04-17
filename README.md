# Project_News_Paper
# Запускаем Shell
Python manage.py shell
from NewsPaper.models import *
# Создаем Пользователей
user1 = User.objects.create_user('Dmitry Kiselev')
user1 = User.objects.create_user('Sergey Titov')
# Создаем объекты модели Author
Author.objects.create(author = user1)
Author.objects.create(author = user2)
# Добавляем категории в модель Category
Category.objects.create(article_category = 'sport') # (id=1)
Category.objects.create(article_category = 'politics') # (id2)
Category.objects.create(article_category = 'education') # (id3)
Category.objects.create(article_category = 'culture')# (id=4)
# Добавляем статьи и новости
# <Post: Post object (1)> - т.е. этот пост имеет id=1
Post.objects.create(
post_author = author, 
category = 'A', 
title = 'The Record', 
content = 'Long text'
)

# <Post: Post object (2)>
Post.objects.create(
post_author = author, 
category = 'A', 
title = 'The Magic', 
content = 'Not very long story'
)

# <Post: Post object (3)>
      Post.objects.create(
post_author = author, 
category = 'N', 
title = 'Some news', 
content = 'We have launched a new project'
)
# Присваиваем им категории
# статье под id=1 присваиваем категорию 1 - ‘sports’
Post.objects.get(id=1).post_category.add(Category.objects.get(id=1))
# статье под id=1 присваиваем категорию 4 - 'culture'
Post.objects.get(id=1).post_category.add(Category.objects.get(id=4))
# Создаем комментарии к объектам модели Post
Comment.objects.create(
                      comment_post=Post.objects.get(id=1),
                      comment_user=Author.objects.get(id=1).author, 
                      feedback_text = 'Very interesting article'
                      )

Comment.objects.create(
                      comment_post=Post.objects.get(id=2), 
                      comment_user = Author.objects.get(id=1).author,
                      feedback_text = 'It is interesting'
                      )

Comment.objects.create(
                      comment_post = Post.objects.get(id=3), 
                      comment_user = Author.objects.get(id=2).author, feedback_text = "Wow, that's awesome"
                      )

Comment.objects.create(
                      comment_post=Post.objects.get(id=1), 
                      comment_user = Author.objects.get(id=2).author, feedback_text = 'It is okay'
                      )
# Корректируем рейтинги объектов с помощью функций like()/dislike()
Comment.objects.get(id=1).like()
Post.objects.get(id=1).dislike()
Post.objects.get(id=3).like()
# проверка рейтинга пользователя
Comment.objects.get(id=1).comment_rate
# проверка рейтинга поста
Post.objects.get(id=1).post_rate
# Обновляем рейтинг Пользователей
u1 = Author.objects.get(id=1)
u1.update_rating()
u1.user_rate

>>> u2 = Author.objects.get(id=2)
чтобы посмотреть суммарный рейтинг комментариев пользователя, можно обратиться к нему напрямую
>>> u2.author.comment_set.aggregate(comment_rating=Sum('comment_rate'))
>>> u2.update_rating()
>>> u2.user_rate
# Выводим имя и рейтинг лучшего Пользователя
>>> s = Author.objects.order_by('user_rate')
>>> for i in s:
...     i.user_rate
...     i.author.username
1
'Sergey Titov'
60
'Dmitry Kiselev'
# Выводим дату добавления, имя автора, рейтинг, заголовок и превью лучшей статьи, основываясь на like()/dislike()
>>> p = Post.objects.order_by('-post_rate')
for i in p[:1]:
...     i.date_created
...     i.post_author.author
...     i.post_rate
...     i.title
...     i.preview()
# Выводим все комментарии к этой статье
>>> Post.objects.all().order_by('-post_rate')[0].comment_set.values(
'comment_date_created', 
'comment_user', 
'comment_rate', 'feedback_text'
)

# В консоли выведутся комментарии:
<QuerySet [
{
'comment_date_created': datetime.date(2021, 5, 11), 
'comment_user': 3, 
'comment_rate': 1, 
'feedback_text': 'Very interesting article'
}, 

{
'comment_date_created': datetime.date(2021, 5, 11), 
'comment_user': 4, 
'comment_rate': 0, 
'feedback_text': 'It is okay'
}, 

{
'comment_date_created': datetime.date(2021, 5, 12), 
'comment_user': 3, 
'comment_rate': 8, 
'feedback_text': 'Very interesting article'
}, 

{
'comment_date_created': datetime.date(2021, 5, 12), 'comment_user': 4, 
'comment_rate': 8, 
'feedback_text': 'It is okay'
}
]
