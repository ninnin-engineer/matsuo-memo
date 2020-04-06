# ActiveModel::Serializers


## ActiveModel::Serializersとは
Railsなどで簡単で素早くjsonを作れるgem。


## jbuilderを使った場合
jbuilderはpartialを使うと遅くなるらしい。
```
# users_controller.rb
def list_with_jbuilder
  @users = User.all
  render :list_with_jbuilder
end
```
```
# list_with_jbuilder.jbuilder
json.array! @users.each do |user|
  json.partial! 'user', user: user
end
```
```
# _user.jbuilder
json.id user.id
json.email user.email
json.name user.display_name
```


## serializerを使った場合
```
# users_controller.rb
def list_with_serializer
  users = User.all
  render json: ActiveModel::ArraySerializer.new(
             users,
             each_serializer: UserSerializer
         ).to_json
end
```
```
# user_serializer
class UserSerializer < ActiveModel::Serializer
  attributes :id, :email, :name

  def name
    object.display_name
  end
end
```
