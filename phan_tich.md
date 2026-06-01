# Phần 1: Phân tích logic

Mặc dù ứng dụng đã tích hợp Spring Security, tài khoản `user` vẫn có thể truy cập được `/admin/orders` vì cấu hình hiện tại chỉ kiểm tra **người dùng đã đăng nhập hay chưa**, chứ chưa kiểm tra **vai trò của người dùng**.

Cấu hình hiện tại:

```java
.authorizeHttpRequests(authorize -> authorize
    .requestMatchers("/").permitAll()
    .anyRequest().authenticated()
)
```

Ý nghĩa:

```java
.requestMatchers("/").permitAll()
```

Cho phép tất cả mọi người truy cập trang chủ `/`.

```java
.anyRequest().authenticated()
```

Tất cả request còn lại chỉ cần **đã đăng nhập** là được truy cập.

Vì `/admin/orders` không có rule riêng như:

```java
.requestMatchers("/admin/**").hasRole("ADMIN")
```

nên đường dẫn `/admin/orders` bị xử lý bởi:

```java
.anyRequest().authenticated()
```

Do đó, chỉ cần tài khoản `user` đăng nhập thành công, dù không có quyền `ADMIN`, vẫn có thể truy cập `/admin/orders`.

Nguyên nhân gốc rễ là:

> Cấu hình `SecurityFilterChain` thiếu rule phân quyền cho nhóm đường dẫn `/admin/**`, nên hệ thống chỉ xác thực đăng nhập mà không kiểm tra vai trò người dùng.

---