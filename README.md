### Написать SQL-запросы для PostgreSQL

**1.Выборки пользователей, у которых количество постов больше, чем у пользователя их пригласившего.**

```sql
SELECT u1.user_id, u1.username
FROM users u1
JOIN invitations i ON u1.user_id = i.invited_user_id
JOIN users u2 ON i.inviting_user_id = u2.user_id
WHERE (SELECT COUNT(*) FROM posts p1 WHERE p1.user_id = u1.user_id) > (SELECT COUNT(*) FROM posts p2 WHERE p2.user_id = u2.user_id);
```

**2.Выборки пользователей, имеющих максимальное количество постов в своей группе.**

```sql
SELECT u1.user_id, u1.username
FROM users u1
JOIN (
    SELECT u.group_id, MAX(post_count) AS max_post_count
    FROM users u
    LEFT JOIN (
        SELECT user_id, COUNT(*) AS post_count
        FROM posts
        GROUP BY user_id
    ) p ON u.user_id = p.user_id
    GROUP BY u.group_id
) max_posts ON u1.group_id = max_posts.group_id AND (SELECT COUNT(*) FROM posts p2 WHERE p2.user_id = u1.user_id) = max_posts.max_post_count;
```

**3.Выборка групп, количество пользователей в которых превышает 10000**

```sql
SELECT group_id, group_name
FROM groups
WHERE (SELECT COUNT(*) FROM users WHERE group_id = groups.group_id) > 10000;
```

**4.Выборка пользователей, у которых пригласивший их пользователь из другой группы**

```sql
SELECT u1.user_id, u1.username
FROM users u1
JOIN invitations i ON u1.user_id = i.invited_user_id
JOIN users u2 ON i.inviting_user_id = u2.user_id
WHERE u1.group_id <> u2.group_id;
```

**5.Выборка групп с максимальным количеством постов у пользователей**

```sql
SELECT g.group_id, g.group_name
FROM groups g
JOIN users u ON g.group_id = u.group_id
JOIN (
    SELECT u.group_id, MAX(post_count) AS max_post_count
    FROM users u
    LEFT JOIN (
        SELECT user_id, COUNT(*) AS post_count
        FROM posts
        GROUP BY user_id
    ) p ON u.user_id = p.user_id
    GROUP BY u.group_id
) max_posts ON g.group_id = max_posts.group_id AND (SELECT COUNT(*) FROM posts p2 WHERE p2.user_id = u.user_id) = max_posts.max_post_count;
```


### Написать SQL-запросы для PostgreSQL добавления трех полей, изменения одного поля и добавления двух индексов в базу данных размером свыше 100 ГБ и более 8 миллионов строк.

**1.Добавление трех полей**

```sql
ALTER TABLE products
ADD COLUMN name varchar,
ADD COLUMN title text,
ADD COLUMN price integer;
```

**2.Изменение поля**

```sql
ALTER TABLE products
ALTER COLUMN title SET DATA TYPE varchar;
```

**3.Добавление индексов**

```sql
CREATE INDEX products_index_name ON name (title);
```

### Реализовать на PHP расчет количества вторников между двумя датами на PHP. Алгоритм должен быть наиболее оптимальный.

```php
function countTuesdays($startDate, $endDate) {
    $start = new DateTime($startDate);
    $end = new DateTime($endDate);
    
    $weeks = floor($start->diff($end)->days / 7);
    $count = $weeks;

    if ($start->format('N') === '2' && $start->diff($end)->days % 7 != 0) {
        $count++;
    }
    if ($end->format('N') === '2' && $start->diff($end)->days % 7 != 0) {
        $count++;
    }

    return $count;
}

$startDate = '2023-01-01';
$endDate = '2023-12-31';

$result = countTuesdays($startDate, $endDate);
```


