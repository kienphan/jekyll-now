---
layout: post
title: Tạo E2E Testing tự động với Rspec, Turnip, Capybara, Selenium
---

### e2e testing là cái gì
- e2e testing hay còn gọi là UI Testing là 1 khâu kiểm thử để kiểm tra tương tác của ứng dụng với dataflow hoạt động có đúng như thiết kế hay không 1 cách toàn diện từ điểm đầu đến điểm cuối. 
- e2e testing là 1 phương thức kiểm thử hộp đen, ngay từ cái tên đã thể hiện điều đó. 
- Việc e2e testing bằng tay sẽ tốn khá nhiều chi phí trong TH ứng dụng lớn với rất nhiều page và form cần tương tác, vì vậy việc việc  e2e testing sẽ giảm thiểu costs(chi phí thời gian, nhân lực)

Bài viết này sẽ giới thiệu cách tạo ra một môi trường Rails E2E test, với việc sử dụng Chrome, Firefox hoặc PhantomJS làm browser.

### Basic Setup

Thêm các gói cần thiết vào `Gemfile`:

```ruby
group :test do
  gem 'turnip'
  gem 'rspec-rails'
  gem "capybara"
  gem 'poltergeist'
  gem 'selenium-webdriver'
end
```

Sau đấy thì chạy `bundle install`

Chắc chắn là đã cài đặt rspec, sẽ sinh ra file `.rspec` với `spec` folder.

```ruby
rails g rspec:install
```

Thêm dòng sau vào `.rspec` để chạy turnip test

```ruby
-r turnip/rspec
```

Bây giờ là viết các kịch bản test tương ứng với các tính năng cần test vào 1 thư mục mới để tách riêng phân biệt với Unit Test

```bash
cd spec
mkdir features
```

Ta có thể viết các feature bằng các ngôn ngữ tự nhiên, rất dễ hiểu. 

####Ví dụ: 
> Chúng ta test 1 feature Login với việc nhập username và password (độ dài password không dưới 6 kí tự) rồi nhấn Submit để login, có 2 kichj bản xảy ra:
>  - Nếu login thành công, redirect về trang mypage
>  - Thất bại, error message sẽ hiển thị ở ngay trang login, các validate client-side được ưu tiên

```bash
touch spec/features/user_login.feature
```

- Xác định các testcase cần thiết: 
  - Login Failure
    - username blank, password blank
    - username OK, password blank
    - username blank, pasword OK
    - username chứa các kí tự đặc biệt như là `\`, `"` (giả sử không được cho phép), password ko quan tâm
    - password dưới 6 kí tự
  - Login Success (VD với user hợp lệ trong DB là kienphan và password là 123456)

Thêm các testcases vào `user_login.feature`

```ruby
Feature: Login 

  Background: 
    Given I am on login page

  Scenario: Login fail when username and password blank
    Given I am on login page
    When I press submit
    Then I see wrong message on login page

  Scenario: Login fail when username OK and password blank
    Given I am on login page
    When I enter username kienphan
    And I press submit
    Then I see wrong message on login page

  Scenario: Login fail when username blank and password OK
    Given I am on login page
    When I enter password 123456
    And I press submit
    Then I see wrong message on login page

  Scenario: Login fail when invalid username
    Given I am on login page
    When I enter username "kien/phan"
    And I enter password 123456
    And I press submit
    Then I see wrong message on login page

  Scenario: Login fail when invalid password
    Given I am on login page
    When I enter username kienphan
    And I enter password 1234
    And I press submit
    Then I see wrong message on login page

  Scenario: Login Success
    Given I am on login page
    When I enter username kienphan
    And I enter password 123456
    And I press submit
    Then I am redirect to mypage
```

Nhìn qua thì khá dễ hiểu, code gì mà như viết văn =)) Trước tiên cần hiểu 1 số khải niệm trong đấy

  - Feature: cái này là feature name, viết sao cho đọc cái là hiểu tính năng gì là được
  - Scenario: kịch bản, coi như là testcase
  - Các phần Given, When, And, Then là các `step`, trình duyệt sẽ lần lượt thực hiện các step này

Ta để ý thấy có khái niệm `step` ở đây, và phải định nghĩa step thì mới có thể run test được

```bash
# Tạo thư mục step
mkdir spec/features/steps
```

Và tiếp đến file định nghĩa các  steps 

```bash
touch spec/features/steps/user_login_steps.rb
```

Viết các steps đã định nghĩa ở `user_login.feature`

```ruby
step 'I am on login page' do
  visit "#{YOUR_HOST}/login"
end

# Kiểm tra phần tử trên trang web, check view để xem ô nhập username có tên là gì
# VD ở đây là <input type='text' name='user_name' ... />
step 'I enter username :username' do |username|
  fill_in 'user_name', with: username
end

# Tương tự với password
# VD ở đây là <input type='password' name='user_password' ... />
step 'I enter password :password' do |password|
  fill_in 'user_password', with: password
end

# VD với button submit có view là <input type='submit' name='submit'... />åååå
step 'I press submit' do
  find('input[name="submit"]').click
end

# Giả sử login có lỗi sẽ có 1 alert text với class .error được hiện ra 
step 'I see wrong message on login page' do
  page.should have_selector '.error', text: 'Login fail lòi, recheck đi bạn ei'
end

step 'I am redirect to mypage' do
  current_path.should =~ /mypage/
end
```

Để chứa các config của `turnip`, ta tạo 1 file `turnip_helper.rb`

```bash
touch spec/turnip_helper
```

```ruby
require 'turnip'
require 'turnip/capybara' # to use Capybara DSL methods in steps
require 'capybara'
require 'selenium-webdriver'
require 'capybara/poltergeist'

Dir.glob( File.expand_path("../features/steps/**/*steps.rb", __FILE__) ){ |f| load f, true  }

Capybara.configure do |config|
  config.javascript_driver = :selenium
  config.current_driver = :selenium
end
```

Bây giờ thì khởi động server lên bằng `rails server`, rồi chạy rspec `bundle exec rake spec`, ta sẽ thấy Firefox tự động bật lên và kết quả test hiện trên màn hình console

### Thay đổi driver

Nếu bạn muốn dùng Chrome thay firefox thì tiến hành đơn giản như sau

Thêm gem chrome vào `Gemfile`

```ruby
gem "chromedriver-helper"
```

Đăng kí driver đến Capybara để nó sử dụng Chrome Driver để test

```ruby
Capybara.register_driver :selenium_chrome do |app|
  Capybara::Selenium::Driver.new(app, :browser => :chrome)
end

Capybara.configure do |config|
  config.javascript_driver = :selenium_chrome
  config.current_driver = :selenium_chrome
end
```

Bây giờ nếu chạy test thì Chrome sẽ bật lên để chạy test tự động