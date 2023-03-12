# 如何用 NestJS 构建一个电子商务应用程序

> 原文：<https://blog.logrocket.com/how-build-ecommerce-app-nestjs/>

NestJS 是构建服务器端应用程序的最佳节点框架之一。在本教程中，我们将探索如何构建一个简单的 NestJS 电子商务应用程序，演示 Nest 的许多主要功能。我们将涵盖:

## 开始使用我们的 NestJS 电子商务应用

默认情况下， [NestJS](https://nestjs.com/) 使用 [Express](http://expressjs.com/) ，尽管你可以选择使用 [Fastify](https://www.fastify.io/) 来代替。Nest 提供了坚实的应用程序架构，而 Express 和 Fastify 是强大的 HTTP 服务器框架，具有无数用于应用程序开发的功能。

拥有健壮的架构使您能够构建高度可伸缩、可测试、松散耦合且易于维护的应用程序。使用 Nest 使您能够[将 Node.js 后端提升到下一个级别](https://blog.logrocket.com/node-back-end-next-level-nestjs/)。

Nest 深受 Angular 的启发，并借用了它的许多概念。如果你已经使用 Angular，Nest 可能是完美的搭配。

要学习本教程，您至少需要具备 Node、MongoDB、TypeScript 和 Nest 的基本知识和经验。确保您的机器上安装了[节点](https://nodejs.org/en/)和 [MongoDB](https://www.mongodb.com/) 。

### 你应该知道的嵌套特性

让我们花点时间回顾一下 Nest 的主要特性:模块、控制器和服务。

模块是组织和构建 Nest app 的主要策略。必须至少有一个根模块才能创建应用程序。每个模块可以包含控制器和服务，甚至其他模块。

Nest 使用依赖注入模式将模块与其依赖项连接起来。为了使一个类可以注入，Nest 使用了一个`@Injectable`装饰器。然后，为了在模块或控制器中提供类，它使用基于构造函数的依赖注入。

控制器处理传入的 HTTP 请求，验证参数，并向客户端返回响应。控制器应该保持干净和简单，这是下一个嵌套特性发挥作用的地方。

服务为您的 Nest 项目保存了大部分业务逻辑和应用程序功能。任何复杂的逻辑都应该通过服务来提供。事实上，服务属于称为提供者的主要类别。

提供者只是一个作为依赖注入的类。可能使用的其他类型的提供者包括像仓库、工厂、助手等类。

### 为我们的电子商务应用程序创建一个新的嵌套项目

准备好之后，让我们初始化一个新的 Nest 项目。首先，我们将安装 Nest CLI。然后，我们将创建一个新项目:

```
npm install -g @nestjs/cli
nest new nestjs-ecommerce

```

安装完成后，导航到项目并启动它:

```
cd nestjs-ecommerce
npm run start:dev

```

然后，您可以通过访问 [http://localhost:3000/](http://localhost:3000/) 在浏览器中启动该应用程序。你应该看到一个漂亮的“你好，世界！”消息。

应用程序将在您做出任何更改后自动重新加载。如果您想手动重启应用程序，请使用`npm run start`命令。

现在，我们准备开始创建商店功能。

## 创建 NestJS 电子商务商店产品功能

在这一部分，我们将重点关注产品管理。商店产品特性将允许我们检索商店产品，添加新的产品，并编辑或删除它们。

### 创造我们的产品资源

让我们从创建所需的资源开始。要创建它们，请运行以下命令:

```
nest g module product
nest g service product --no-spec
nest g controller product --no-spec 

```

第一个命令生成一个产品模块，并将其放在自己的同名目录中。

接下来的两个命令生成服务和控制器文件，并将它们自动导入到`product`模块中。`--no-spec`参数告诉 Nest 我们不想生成额外的测试文件。

运行以上命令后，我们将得到一个新的`product`目录，其中包含以下文件:`product.module.ts`、`product.service.ts`和`product.controller.ts`。

现在我们有了 NestJS 电子商务商店产品功能的基本结构。在我们继续之前，我们需要建立我们的数据库。

### 配置 MongoDB 数据库

由于我们使用 MongoDB 作为数据库，我们需要安装`mongoose`和`@nestjs/mongoose`包。

```
npm install --save @nestjs/mongoose mongoose

```

安装完成后，打开`app.module.ts`并用以下内容替换其内容:

```
import { Module } from '@nestjs/common';
import { MongooseModule } from '@nestjs/mongoose'; // 1.1 Import the mongoose module
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { ProductModule } from './product/product.module'; // 2.1 Import the product module

@Module({
  imports: [
    MongooseModule.forRoot('mongodb://localhost/store'), // 1.2 Setup the database
    ProductModule, // 2.2 Add the product module
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}

```

下面是我们在上面的代码中所做的。使用我编号的笔记继续学习:

*   首先，我们导入了`MongooseModule` (1.1)，并用它来建立一个新的`store`数据库(1.2)
*   其次，我们导入了`ProductModule` (2.1)并将其添加到了`imports`数组(2.2)

我们的下一步是为我们的产品模型创建一个数据库模式。

### 创建产品模型架构

在`product`目录中，创建一个新的`schemas`目录。在新目录下放一个`product.schema.ts`文件，内容如下:

```
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import { Document } from 'mongoose';

export type ProductDocument = Product & Document;

@Schema()
export class Product {
  @Prop()
  name: string;

  @Prop()
  description: string;

  @Prop()
  price: number;

  @Prop()
  category: string;
}

export const ProductSchema = SchemaFactory.createForClass(Product);

```

上面的代码用`name`、`description`、`price`和`category`属性为我们的产品创建了一个模式。

现在按以下方式编辑`product.module.ts`:

```
import { Module } from '@nestjs/common';
import { ProductController } from './product.controller';
import { ProductService } from './product.service';
import { MongooseModule } from '@nestjs/mongoose'; // 1\. Import mongoose module
import { ProductSchema } from './schemas/product.schema'; // 2\. Import product schema

@Module({
  imports: [
    MongooseModule.forFeature([{ name: 'Product', schema: ProductSchema }]) // 3\. Setup the mongoose module to use the product schema
  ],
  controllers: [ProductController],
  providers: [ProductService]
})
export class ProductModule {}

```

正如你从我编号的笔记中看到的，在上面的代码中，我们导入了`MongooseModule` (1)和`ProductModule` (2)，然后设置`ProductSchema`用于我们的产品模型(3)。

### 创建产品 DTO 文件

除了产品模式，我们还需要为我们的 NestJS 电子商务应用程序的两个数据传输对象(DTO)文件。DTO 文件定义了从表单提交、搜索查询等接收的数据。

我们需要一个 DTO 用于产品创建，另一个用于产品过滤。让我们现在创建它们。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在`product`目录中，创建一个新的`dtos`目录。在这个新目录中放一个`create-product.dto.ts`文件，内容如下:

```
export class CreateProductDTO {
  name: string;
  description: string;
  price: number;
  category: string;
}

```

上面的 DTO 定义了一个产品对象，它具有创建新产品所必需的属性。

然后，在同一个目录下，创建一个包含以下内容的`filter-product.dto.ts`文件:

```
export class FilterProductDTO {
  search: string;
  category: string;
}

```

第二个 DTO 定义了一个过滤器对象，我们将使用它通过搜索查询、类别或两者来过滤商店产品。

### 创建产品服务方法

本节的所有准备工作都已完成。现在让我们创建产品管理的实际代码。

打开`product.service.ts`文件，将其内容替换为以下内容:

```
import { Injectable } from '@nestjs/common';
import { Model } from 'mongoose';
import { InjectModel } from '@nestjs/mongoose';
import { Product, ProductDocument } from './schemas/product.schema';
import { CreateProductDTO } from './dtos/create-product.dto';
import { FilterProductDTO } from './dtos/filter-product.dto';

@Injectable()
export class ProductService {
  constructor(@InjectModel('Product') private readonly productModel: Model<ProductDocument>) { }

  async getFilteredProducts(filterProductDTO: FilterProductDTO): Promise<Product[]> {
    const { category, search } = filterProductDTO;
    let products = await this.getAllProducts();

    if (search) {
      products = products.filter(product => 
        product.name.includes(search) ||
        product.description.includes(search)
      );
    }

    if (category) {
      products = products.filter(product => product.category === category)
    }

    return products;
  }

  async getAllProducts(): Promise<Product[]> {
    const products = await this.productModel.find().exec();
    return products;
  }

  async getProduct(id: string): Promise<Product> {
    const product = await this.productModel.findById(id).exec();
    return product;
  }

  async addProduct(createProductDTO: CreateProductDTO): Promise<Product> {
    const newProduct = await this.productModel.create(createProductDTO);
    return newProduct.save();
  }

  async updateProduct(id: string, createProductDTO: CreateProductDTO): Promise<Product> {
    const updatedProduct = await this.productModel
      .findByIdAndUpdate(id, createProductDTO, { new: true });
    return updatedProduct;
  }

  async deleteProduct(id: string): Promise<any> {
    const deletedProduct = await this.productModel.findByIdAndRemove(id);
    return deletedProduct;
  }
}

```

让我们一段一段地检查上面的代码块。

首先，让我们看看下面复制的部分:

```
@Injectable()
export class ProductService {
  constructor(@InjectModel('Product') private readonly productModel: Model<ProductDocument>) { }

}

```

这段代码通过使用`@InjectModel`装饰器注入所需的依赖项(产品模型)。

在下一节中，我们有两种方法:

```
async getAllProducts(): Promise<Product[]> {
  const products = await this.productModel.find().exec();
  return products;
}

async getProduct(id: string): Promise<Product> {
  const product = await this.productModel.findById(id).exec();
  return product;
}

```

第一种方法`getAllProducts`是获取所有产品。第二种方法`getProduct`是为了获得单一产品。我们使用标准的猫鼬方法来完成这些动作。

下面的方法`getFilteredProducts`返回过滤后的产品:

```
async getFilteredProducts(filterProductDTO: FilterProductDTO): Promise<Product[]> {
  const { category, search } = filterProductDTO;
  let products = await this.getAllProducts();

  if (search) {
    products = products.filter(product => 
      product.name.includes(search) ||
      product.description.includes(search)
    );
  }

  if (category) {
    products = products.filter(product => product.category === category)
  }

  return products;
}

```

可以通过搜索查询、类别或两者来过滤产品。

下面的下一个方法`addProduct`创建了一个新产品:

```
async addProduct(createProductDTO: CreateProductDTO): Promise<Product> {
  const newProduct = await this.productModel.create(createProductDTO);
  return newProduct.save();
}

```

`addProduct`通过使用来自`create-product.dto.ts`文件的类并将其保存到数据库来实现这一点。

最后两种方法是`updateProduct`和`deleteProduct`:

```
async updateProduct(id: string, createProductDTO: CreateProductDTO): Promise<Product> {
  const updatedProduct = await this.productModel
    .findByIdAndUpdate(id, createProductDTO, { new: true });
  return updatedProduct;
}

async deleteProduct(id: string): Promise<any> {
  const deletedProduct = await this.productModel.findByIdAndRemove(id);
  return deletedProduct;
}

```

使用这些方法，您可以通过 ID 找到一个产品，并从数据库中更新或删除它。

### 创建产品控制器方法

产品模块的最后一步是创建 API 端点。

我们将创建以下 API 端点:

*   发布`store/products/` —添加新产品
*   获取`store/products/` —获取所有产品
*   获取`store/products/:id` —获取单个产品
*   放置`store/products/:id` —编辑单个产品
*   删除`store/products/:id` —删除单个产品

打开`product.controller.ts`文件，将其内容替换为以下内容:

```
import { Controller, Post, Get, Put, Delete, Body, Param, Query, NotFoundException } from '@nestjs/common';
import { ProductService } from './product.service';
import { CreateProductDTO } from './dtos/create-product.dto';
import { FilterProductDTO } from './dtos/filter-product.dto';

@Controller('store/products')
export class ProductController {
  constructor(private productService: ProductService) { }

  @Get('/')
  async getProducts(@Query() filterProductDTO: FilterProductDTO) {
    if (Object.keys(filterProductDTO).length) {
      const filteredProducts = await this.productService.getFilteredProducts(filterProductDTO);
      return filteredProducts;
    } else {
      const allProducts = await this.productService.getAllProducts();
      return allProducts;
    }
  }

  @Get('/:id')
  async getProduct(@Param('id') id: string) {
    const product = await this.productService.getProduct(id);
    if (!product) throw new NotFoundException('Product does not exist!');
    return product;
  }

  @Post('/')
  async addProduct(@Body() createProductDTO: CreateProductDTO) {
    const product = await this.productService.addProduct(createProductDTO);
    return product;
  }

  @Put('/:id')
  async updateProduct(@Param('id') id: string, @Body() createProductDTO: CreateProductDTO) {
    const product = await this.productService.updateProduct(id, createProductDTO);
    if (!product) throw new NotFoundException('Product does not exist!');
    return product;
  }

  @Delete('/:id')
  async deleteProduct(@Param('id') id: string) {
    const product = await this.productService.deleteProduct(id);
    if (!product) throw new NotFoundException('Product does not exist');
    return product;
  }
}

```

NestJS 提供了一整套 JavaScript 装饰器来处理 HTTP 请求和响应(`Get`、`Put`、`Body`、`Param`等)。)，处理错误(`NotFoundException`)，定义控制器(`Controller`，等等。

我们从文件开头的`@nestjs/common`导入了我们需要的。我们还导入我们已经创建并且需要的所有其他文件:`ProductService`、`CreateProductDTO`和`FilterProductDTO`。

从现在开始，我就不详细解释进口了。大多数都非常简单明了。关于特定类或组件使用的更多信息，你可以[查阅文档](https://www.typescriptlang.org/docs/handbook/decorators.html)。

让我们把剩下的代码分成更小的块。

首先，我们使用`@Controller` decorator 来设置由所有端点共享的 URL 部分:

```
@Controller('store/products')
export class ProductController {
  constructor(private productService: ProductService) { }
}

```

我们还在上面的代码中的类构造函数中注入了产品服务。

接下来，我们通过使用`@Get`装饰器来定义以下端点:

```
@Get('/')
async getProducts(@Query() filterProductDTO: FilterProductDTO) {
  if (Object.keys(filterProductDTO).length) {
    const filteredProducts = await this.productService.getFilteredProducts(filterProductDTO);
    return filteredProducts;
  } else {
    const allProducts = await this.productService.getAllProducts();
    return allProducts;
  }
}

```

定义完端点后，我们在`getProducts()`方法中使用`@Query`装饰器，并使用来自`filter-product.dto.ts`的对象从请求中获取查询参数。

如果来自请求的查询参数存在，我们使用来自产品服务的`getFilteredProduct()`方法。如果没有这样的参数，我们使用常规的`getAllProducts()`方法。

在下面的端点中，我们使用`@Body`装饰器从请求体中获取所需的数据，然后将其传递给`addProduct()`方法:

```
@Post('/')
async addProduct(@Body() createProductDTO: CreateProductDTO) {
  const product = await this.productService.addProduct(createProductDTO);
  return product;
}

```

在接下来的端点中，我们使用`@Param`装饰器从 URL 获取产品 ID:

```
@Get('/:id')
async getProduct(@Param('id') id: string) {
  const product = await this.productService.getProduct(id);
  if (!product) throw new NotFoundException('Product does not exist!');
  return product;
}

@Put('/:id')
async updateProduct(@Param('id') id: string, @Body() createProductDTO: CreateProductDTO) {
  const product = await this.productService.updateProduct(id, createProductDTO);
  if (!product) throw new NotFoundException('Product does not exist!');
  return product;
}

@Delete('/:id')
async deleteProduct(@Param('id') id: string) {
  const product = await this.productService.deleteProduct(id);
  if (!product) throw new NotFoundException('Product does not exist');
  return product;
}

```

然后，我们使用产品服务中的适当方法来获取、编辑或删除产品。如果没有找到产品，我们使用`NotFoundException`抛出一条错误消息。

## 创建用户管理功能

我们需要为 NestJS 电子商务应用程序创建的下一个功能是用户管理功能。

### 生成我们的用户管理资源

对于用户管理特性，我们只需要一个模块和一个服务。要创建它们，请运行以下命令:

```
nest g module user
nest g service user --no-spec 

```

和前面的特性一样，我们需要一个模式和 DTO。

### 创建用户方案和 DTO

在 Nest 生成的`user`目录下，新建一个`schemas`文件夹。在这个新文件夹中添加一个`user.schema.ts`文件，内容如下:

```
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import { Document } from 'mongoose';
// import { Role } from 'src/auth/enums/role.enum';

export type UserDocument = User & Document;

@Schema()
export class User {
  @Prop()
  username: string;

  @Prop()
  email: string;

  @Prop()
  password: string;

/*
  @Prop()
  roles: Role[];
*/
}

export const UserSchema = SchemaFactory.createForClass(User);

```

当我们实现用户授权时，将使用代码块末尾的注释代码。我将在本教程的后面告诉你什么时候取消注释它们。

接下来，在`user`目录中，创建一个新的`dtos`文件夹。在这个新文件夹中添加一个`create-user-dto.ts`文件，内容如下:

```
export class CreateUserDTO {
  username: string;
  email: string;
  password: string;
  roles: string[];
}

```

### 配置资源

打开`user.module.ts`,按照我们对产品特性所做的相同方式设置模式:

```
import { Module } from '@nestjs/common';
import { MongooseModule } from '@nestjs/mongoose';
import { UserSchema } from './schemas/user.schema';
import { UserService } from './user.service';

@Module({
  imports: [
    MongooseModule.forFeature([{ name: 'User', schema: UserSchema }])
  ],
  providers: [UserService],
  exports: [UserService]
})
export class UserModule {}

```

在上面的代码中，我们还导出了`UserService`,以便稍后在认证服务中使用它。

我们还需要安装另外两个包:`bcrypt`和`@types/bcrypt`:

```
npm install bcrypt
npm install -D @types/bcrypt

```

这些包使我们能够保存密码，这将在下一节中讨论。

### 创建用户服务方法

现在让我们添加用户管理的逻辑。打开`user.service.ts`文件，将其内容替换为以下内容:

```
import { Injectable } from '@nestjs/common';
import { Model } from 'mongoose';
import { InjectModel } from '@nestjs/mongoose';
import { User, UserDocument } from './schemas/user.schema';
import { CreateUserDTO } from './dtos/create-user.dto';
import * as bcrypt from 'bcrypt';

@Injectable()
export class UserService {
  constructor(@InjectModel('User') private readonly userModel: Model<UserDocument>) { }

  async addUser(createUserDTO: CreateUserDTO): Promise<User> {
    const newUser = await this.userModel.create(createUserDTO);
    newUser.password = await bcrypt.hash(newUser.password, 10);
    return newUser.save();
  }

  async findUser(username: string): Promise<User | undefined> {
    const user = await this.userModel.findOne({username: username});
    return user;
  }
}

```

我们在上面的代码中添加了两个方法。`addUser()`方法创建一个新用户，使用`bcrypt.hash()`加密新用户的密码，然后将用户保存到数据库。

`findUser()`方法通过`username`找到特定的用户。

## 创建用户验证和授权

在本节中，我们将扩展我们的 NestJS 电子商务应用程序中的用户管理特性，添加[用户认证，用于验证用户的身份](https://docs.nestjs.com/security/authentication)，以及[用户授权，用于定义用户可以做什么](https://docs.nestjs.com/security/authorization)。

我们将使用[众所周知的 Passport 库，](https://blog.logrocket.com/using-passport-authentication-node-js/)，它提供了各种各样的认证策略。让我们安装必要的软件包:

```
npm install --save @nestjs/passport passport passport-local
npm install --save-dev @types/passport-local

```

在上面的代码中，我们安装了主`passport`包、`passport-local`策略(它实现了一个简单的用户名和密码认证机制)和 Nest passport 适配器。我们还安装了`passport-local`的型号。

我们还需要安装用于管理环境变量的`dotenv`包:

```
npm install dotenv

```

在根目录中创建一个`.env`文件，并将以下代码放入其中:

```
JWT_SECRET="topsecret"

```

我们稍后会用到这个变量。

### 生成我们的用户认证和授权资源

像往常一样，让我们首先为我们的 auth 特性创建所需的资源:

```
nest g module auth
nest g service auth --no-spec 
nest g controller auth --no-spec 

```

### 创建用户服务方法

打开`auth.service.ts`文件，将其内容替换为以下内容:

```
import { Injectable } from '@nestjs/common';
import { UserService } from '../user/user.service';
import * as bcrypt from 'bcrypt';

@Injectable()
export class AuthService {
  constructor(private readonly userService: UserService) {}

  async validateUser(username: string, password: string): Promise<any> {
    const user = await this.userService.findUser(username);
    const isPasswordMatch = await bcrypt.compare(
      password,
      user.password
    );
    if (user && isPasswordMatch) {
      return user;
    }
    return null;
  }
}

```

上面的代码为我们提供了一个用户验证方法，它检索用户并验证用户的密码。

### 创建本地身份验证策略

在`auth`目录下，新建一个`strategies`文件夹。在这个新文件夹中添加一个`local.strategy.ts`文件，内容如下:

```
import { Strategy } from 'passport-local';
import { PassportStrategy } from '@nestjs/passport';
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { AuthService } from '../auth.service';

@Injectable()
export class LocalStrategy extends PassportStrategy(Strategy) {
  constructor(private authService: AuthService) {
    super();
  }

  async validate(username: string, password: string): Promise<any> {
    const user = await this.authService.validateUser(username, password);
    if (!user) {
      throw new UnauthorizedException();
    }
    return user;
  }
}

```

这段代码做了两件事。

首先，它调用构造函数中的`super()`方法。如果需要，我们可以在这里传递一个选项对象。我们稍后会看一个例子。

其次，我们添加了一个`validate()`方法，它使用来自 auth 服务的`validateUser()`来验证用户。

### 使用 JWT 创建身份验证策略

现在我们将使用 JSON Web 令牌(JWT) 创建一个 passport [认证策略。这将为已登录的用户返回一个 JWT，供后续调用受保护的 API 端点时使用。](https://blog.logrocket.com/jwt-authentication-best-practices/)

让我们安装必要的软件包:

```
npm install --save @nestjs/jwt passport-jwt
npm install --save-dev @types/passport-jwt

```

接下来，在`strategies`目录中，创建一个包含以下内容的`jwt.strategy.ts`文件:

```
import { ExtractJwt, Strategy } from 'passport-jwt';
import { PassportStrategy } from '@nestjs/passport';
import { Injectable } from '@nestjs/common';
import 'dotenv/config'

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor() {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: process.env.JWT_SECRET,
    });
  }

  async validate(payload: any) {
    return { userId: payload.sub, username: payload.username, roles: payload.roles };
  }
}

```

在上面的代码中，我们用以下属性设置了一个`options`对象:

*   `jwtFromRequest`告诉 Passport 模块如何从请求中提取 JWT(在本例中，作为不记名令牌)
*   `ignoreExpiration`设置为`false`意味着确保 JWT 没有过期的责任被委托给护照模块
*   `secretOrKey`用于签署令牌

`validate()`方法返回一个`payload`，它是被解码为 JSON 的 JWT。然后，我们使用这个有效负载返回一个具有必要属性的用户对象。

现在让我们修改`auth.service.ts`文件:

```
import { Injectable } from '@nestjs/common';
import { UserService } from '../user/user.service';
import { JwtService } from '@nestjs/jwt'; // 1
import * as bcrypt from 'bcrypt';

@Injectable()
export class AuthService {
  constructor(private readonly userService: UserService, private readonly jwtService: JwtService) {} // 2

  async validateUser(username: string, password: string): Promise<any> {
    const user = await this.userService.findUser(username);
    const isPasswordMatch = await bcrypt.compare(
      password,
      user.password
    );
    if (user && isPasswordMatch) {
      return user;
    }
    return null;
  }

  async login(user: any) {
    const payload = { username: user.username, sub: user._id, roles: user.roles };
    return {
      access_token: this.jwtService.sign(payload),
    };
  }
}

```

上面的代码有标签，所以你可以按照我们做的:

*   导入了`JwtService`(参见`//1`)
*   将`JwtService`添加到构造函数中(参见`//2`)。

然后，我们使用`login()`方法来签署 JWT。

在我们做了所有的更改之后，我们需要以下面的方式更新`auth.module.ts`:

```
import { Module } from '@nestjs/common';
import { AuthService } from './auth.service';
import { UserModule } from 'src/user/user.module';
import { PassportModule } from '@nestjs/passport';
import { LocalStrategy } from './strategies/local.strategy';
import { JwtStrategy } from './strategies/jwt.strategy';
import { AuthController } from './auth.controller';
import { JwtModule } from '@nestjs/jwt';
import 'dotenv/config'

@Module({
  imports: [
    UserModule, 
    PassportModule,     
    JwtModule.register({
      secret: process.env.JWT_SECRET,
      signOptions: { expiresIn: '3600s' },
    }),
  ],
  providers: [
    AuthService, 
    LocalStrategy, 
    JwtStrategy
  ],
  controllers: [AuthController],
})
export class AuthModule {}

```

在上面的代码中，我们在`imports`数组中添加了`UserModule`、`PassportModule`和`JwtModule`。

我们还使用了`register()`方法来提供必要的选项:`secret`键和`signOptions`对象，它们将令牌到期时间设置为`3600s`，即 1 小时。

最后，我们在`providers`数组中添加了`LocalStrategy`和`JwtStrategy`。

### 创建当地和 JWT 警卫队

为了使用我们刚刚创建的策略，我们需要创建[守卫](https://docs.nestjs.com/guards)。

在`auth`目录下，新建一个`guards`文件夹。在这个新文件夹中添加一个`local.guard.ts`文件，内容如下:

```
import { Injectable } from '@nestjs/common';
import { AuthGuard } from '@nestjs/passport';

@Injectable()
export class LocalAuthGuard extends AuthGuard('local') {}

```

同样在`guards`文件夹中，创建一个包含以下内容的`jwt.guard.ts`文件:

```
import { Injectable } from '@nestjs/common';
import { AuthGuard } from '@nestjs/passport';

@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt') {}

```

我们马上会看到如何使用这些保护装置。但是首先，让我们创建用户授权功能。

### 创建用户角色管理

为了在我们的 NestJS 电子商务应用程序中实现这个特性，我们将使用基于角色的访问控制。

对于这个特性，我们需要三个文件:`role.enum.ts`、`roles.decorator.ts`和`roles.guard.ts`。先说`role.enum.ts`文件。

在`auth`目录下，新建一个`enums`文件夹。在这个新文件夹中添加一个`role.enum.ts`文件，内容如下:

```
export enum Role {
  User = 'user',
  Admin = 'admin',
}

```

这代表注册用户可用的角色。

现在你可以回到我们之前创建的`[user.schema.ts](#creating-user-schema-dto)` [文件](#creating-user-schema-dto)，取消注释代码。

接下来，在`auth`目录中，创建一个新的`decorators`文件夹。在这个新文件夹中添加一个`roles.decorator.ts`文件，内容如下:

```
import { SetMetadata } from '@nestjs/common';
import { Role } from '../enums/role.enum';

export const ROLES_KEY = 'roles';
export const Roles = (...roles: Role[]) => SetMetadata(ROLES_KEY, roles);

```

在上面的代码中，我们使用`SetMetadata()`来创建装饰器。

最后，在`guards`目录中，创建一个包含以下内容的`roles.guard.ts`文件:

```
import { Injectable, CanActivate, ExecutionContext } from '@nestjs/common';
import { Reflector } from '@nestjs/core';
import { Role } from '../enums/role.enum';
import { ROLES_KEY } from '../decorators/roles.decorator';

@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const requiredRoles = this.reflector.getAllAndOverride<Role[]>(ROLES_KEY, [
      context.getHandler(),
      context.getClass(),
    ]);
    if (!requiredRoles) {
      return true;
    }
    const { user } = context.switchToHttp().getRequest();
    return requiredRoles.some((role) => user.roles?.includes(role));
  }
}

```

在上面的代码中，我们使用了`[Reflector](https://docs.nestjs.com/fundamentals/execution-context#reflection-and-metadata)` [助手类](https://docs.nestjs.com/fundamentals/execution-context#reflection-and-metadata)来访问路由的角色。我们还使用`switchToHttp()`将执行上下文切换到 HTTP，以使用`getRequest()`获得`user`细节。最后，我们返回了用户的角色。

### 控制器方法

本节的最后一步是创建控制器方法。打开`auth.controller.ts`文件，将其内容替换为以下内容:

```
import { Controller, Request, Get, Post, Body, UseGuards } from '@nestjs/common';
import { CreateUserDTO } from 'src/user/dtos/create-user.dto';
import { UserService } from 'src/user/user.service';
import { AuthService } from './auth.service';
import { LocalAuthGuard } from './guards/local-auth.guard';
import { JwtAuthGuard } from './guards/jwt-auth.guard';
import { Roles } from './decorators/roles.decorator';
import { Role } from './enums/role.enum';
import { RolesGuard } from './guards/roles.guard';

@Controller('auth')
export class AuthController {
  constructor(private authService: AuthService, private userService: UserService) {}

  @Post('/register')
  async register(@Body() createUserDTO: CreateUserDTO) {
    const user = await this.userService.addUser(createUserDTO);
    return user;
  }

  @UseGuards(LocalAuthGuard)
  @Post('/login')
  async login(@Request() req) {
    return this.authService.login(req.user);
  }

  @UseGuards(JwtAuthGuard, RolesGuard)
  @Roles(Role.User)
  @Get('/user')
  getProfile(@Request() req) {
    return req.user;
  }

  @UseGuards(JwtAuthGuard, RolesGuard)
  @Roles(Role.Admin)
  @Get('/admin')
  getDashboard(@Request() req) {
    return req.user;
  }
}

```

上面的代码中有四个端点:

*   POST `auth/register`用于创建新用户
*   POST `auth/login`用于登录注册用户
    *   为了验证用户，我们使用了`LocalAuthGuard`
*   GET `auth/user`用于访问用户的个人资料
    *   我们使用`JwtGuard`来认证用户
    *   我们使用`RolesGuard`加`@Roles`装饰器来根据用户的角色提供适当的授权
*   GET `auth/admin`用于访问管理仪表板
*   *   我们还使用了`JwtGuard`和`RolesGuard`，就像在之前的端点中所做的那样

## 为我们的 NestJS 电子商务应用程序创建商店购物车功能

我们将添加到项目中的最后一个特性是一个基本的购物车功能。

### 创建我们的购物车资源

让我们创建下一部分所需的资源:

```
nest g module cart
nest g service cart --no-spec 
nest g controller cart --no-spec 

```

### 创建模式和 dto

对于商店购物车特性，我们需要两个模式:一个描述购物车中的产品，另一个描述购物车本身。

像往常一样，在`cart`目录中，创建一个新的`schemas`文件夹。在这个新文件夹中添加一个`item.schema.ts`文件，内容如下:

```
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import { Document, SchemaTypes } from 'mongoose';

export type ItemDocument = Item & Document;

@Schema()
export class Item {
  @Prop({ type: SchemaTypes.ObjectId, ref: 'Product' })
  productId: string;

  @Prop()
  name: string;

  @Prop()
  quantity: number;

  @Prop()
  price: number;

  @Prop()
  subTotalPrice: number; 
}

export const ItemSchema = SchemaFactory.createForClass(Item);

```

在上面的代码中，在`productId`属性的`@Prop`装饰器中，我们定义了一个对象 id 模式类型，并添加了对产品的引用。这意味着我们将使用产品的 id 作为`productId`值。

下一个模式是针对购物车的。在`schemas`目录下，创建一个包含以下内容的`cart.schema.ts`文件:

```
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import { Document, SchemaTypes } from 'mongoose';
import { Item } from './item.schema';

export type CartDocument = Cart & Document;

@Schema()
export class Cart {
  @Prop({ type: SchemaTypes.ObjectId, ref: 'User' })
  userId: string;

  @Prop()
  items: Item[];

  @Prop()
  totalPrice: number; 
}

export const CartSchema = SchemaFactory.createForClass(Cart);

```

这里，我们对属性`userId`使用相同的技术，它将获得用户 id 的值。对于`items`属性，我们使用我们的`Item`模式来定义一个类型为`Item`的项目数组。

最后，让我们创建项目 DTO。在`user`目录下，新建一个`dtos`文件夹，添加一个`item.dto.ts`文件，内容如下:

```
export class ItemDTO {
  productId: string;
  name: string;
  quantity: number;
  price: number;
}

```

### 配置购物车模块

在我们转到业务逻辑之前，我们需要将购物车模式添加到购物车模块中。打开`cart.module.ts`文件，并将其配置为使用购物车模式，如下所示:

```
import { Module } from '@nestjs/common';
import { CartController } from './cart.controller';
import { CartService } from './cart.service';
import { MongooseModule } from '@nestjs/mongoose';
import { CartSchema } from './schemas/cart.schema';

@Module({
  imports: [
    MongooseModule.forFeature([{ name: 'Cart', schema: CartSchema }])
  ],
  controllers: [CartController],
  providers: [CartService]
})
export class CartModule {}

```

### 创建购物车服务方法

现在让我们创建购物车管理逻辑。打开`cart.service.ts`文件，将其内容替换为以下内容:

```
import { Injectable } from '@nestjs/common';
import { Model } from 'mongoose';
import { InjectModel } from '@nestjs/mongoose';
import { Cart, CartDocument } from './schemas/cart.schema';
import { ItemDTO } from './dtos/item.dto';

@Injectable()
export class CartService {
  constructor(@InjectModel('Cart') private readonly cartModel: Model<CartDocument>) { }

  async createCart(userId: string, itemDTO: ItemDTO, subTotalPrice: number, totalPrice: number): Promise<Cart> {
    const newCart = await this.cartModel.create({
      userId,
      items: [{ ...itemDTO, subTotalPrice }],
      totalPrice
    });
    return newCart;
  }

  async getCart(userId: string): Promise<CartDocument> {
    const cart = await this.cartModel.findOne({ userId });
    return cart;
  }

  async deleteCart(userId: string): Promise<Cart> {
    const deletedCart = await this.cartModel.findOneAndRemove({ userId });
    return deletedCart;
  }

  private recalculateCart(cart: CartDocument) {
    cart.totalPrice = 0;
    cart.items.forEach(item => {
      cart.totalPrice += (item.quantity * item.price);
    })
  }

  async addItemToCart(userId: string, itemDTO: ItemDTO): Promise<Cart> {
    const { productId, quantity, price } = itemDTO;
    const subTotalPrice = quantity * price;

    const cart = await this.getCart(userId);

    if (cart) {
      const itemIndex = cart.items.findIndex((item) => item.productId == productId);

      if (itemIndex > -1) {
        let item = cart.items[itemIndex];
        item.quantity = Number(item.quantity) + Number(quantity);
        item.subTotalPrice = item.quantity * item.price;

        cart.items[itemIndex] = item;
        this.recalculateCart(cart);
        return cart.save();
      } else {
        cart.items.push({ ...itemDTO, subTotalPrice });
        this.recalculateCart(cart);
        return cart.save();
      }
    } else {
      const newCart = await this.createCart(userId, itemDTO, subTotalPrice, price);
      return newCart;
    }
  }

  async removeItemFromCart(userId: string, productId: string): Promise<any> {
    const cart = await this.getCart(userId);

    const itemIndex = cart.items.findIndex((item) => item.productId == productId);

    if (itemIndex > -1) {
      cart.items.splice(itemIndex, 1);
      return cart.save();
    }
  }
}

```

这里有很多方法。让我们一个一个地检查它们。

第一个是为当前用户创建一个新的购物车:

```
async createCart(userId: string, itemDTO: ItemDTO, subTotalPrice: number, totalPrice: number): Promise<Cart> {
  const newCart = await this.cartModel.create({
    userId,
    items: [{ ...itemDTO, subTotalPrice }],
    totalPrice
  });
  return newCart;
}

```

接下来的两种方法用于获取或删除特定用户的购物车:

```
async getCart(userId: string): Promise<CartDocument> {
  const cart = await this.cartModel.findOne({ userId });
  return cart;
}

async deleteCart(userId: string): Promise<Cart> {
  const deletedCart = await this.cartModel.findOneAndRemove({ userId });
  return deletedCart;
}

```

下一种方法是在添加或删除商品时，或者在商品数量发生变化时，重新计算购物车总额:

```
private recalculateCart(cart: CartDocument) {
  cart.totalPrice = 0;
  cart.items.forEach(item => {
    cart.totalPrice += (item.quantity * item.price);
  })
}

```

下一个方法是向购物车添加商品:

```
async addItemToCart(userId: string, itemDTO: ItemDTO): Promise<Cart> {
  const { productId, quantity, price } = itemDTO;
  const subTotalPrice = quantity * price;

  const cart = await this.getCart(userId);

  if (cart) {
    const itemIndex = cart.items.findIndex((item) => item.productId == productId);

    if (itemIndex > -1) {
      let item = cart.items[itemIndex];
      item.quantity = Number(item.quantity) + Number(quantity);
      item.subTotalPrice = item.quantity * item.price;

      cart.items[itemIndex] = item;
      this.recalculateCart(cart);
      return cart.save();
    } else {
      cart.items.push({ ...itemDTO, subTotalPrice });
      this.recalculateCart(cart);
      return cart.save();
    }
  } else {
    const newCart = await this.createCart(userId, itemDTO, subTotalPrice, price);
    return newCart;
  }
}

```

在上面的方法中，如果购物车存在，有两个选项:

1.  产品存在，所以我们需要更新它的数量和小计价格
2.  产品不存在，所以我们需要添加它

无论哪种方式，我们都需要运行`recalculateCart()`方法来适当地更新购物车。如果购物车不存在，我们需要创建一个新的。

最后一种方法是从购物车中删除商品:

```
async removeItemFromCart(userId: string, productId: string): Promise<any> {
  const cart = await this.getCart(userId);

  const itemIndex = cart.items.findIndex((item) => item.productId == productId);

  if (itemIndex > -1) {
    cart.items.splice(itemIndex, 1);
    this.recalculateCart(cart);
    return cart.save();
  }
}

```

与前面的方法类似，在上面的方法中，我们运行`recalculateCart()`来在商品被移除后正确地更新购物车。

### 创建购物车控制器方法

我们完成这个 NestJS 电子商务应用程序项目的最后一步是添加购物车控制器方法。

打开`cart.controller.ts`文件，用以下内容替换其内容:

```
import { Controller, Post, Body, Request, UseGuards, Delete, NotFoundException, Param } from '@nestjs/common';
import { Roles } from 'src/auth/decorators/roles.decorator';
import { Role } from 'src/auth/enums/role.enum';
import { JwtAuthGuard } from 'src/auth/guards/jwt-auth.guard';
import { RolesGuard } from 'src/auth/guards/roles.guard';
import { CartService } from './cart.service';
import { ItemDTO } from './dtos/item.dto';

@Controller('cart')
export class CartController {
  constructor(private cartService: CartService) { }

  @UseGuards(JwtAuthGuard, RolesGuard)
  @Roles(Role.User)
  @Post('/')
  async addItemToCart(@Request() req, @Body() itemDTO: ItemDTO) {
    const userId = req.user.userId;
    const cart = await this.cartService.addItemToCart(userId, itemDTO);
    return cart;
  }

  @UseGuards(JwtAuthGuard, RolesGuard)
  @Roles(Role.User)
  @Delete('/')
  async removeItemFromCart(@Request() req, @Body() { productId }) {
    const userId = req.user.userId;
    const cart = await this.cartService.removeItemFromCart(userId, productId);
    if (!cart) throw new NotFoundException('Item does not exist');
    return cart;
  }

  @UseGuards(JwtAuthGuard, RolesGuard)
  @Roles(Role.User)
  @Delete('/:id')
  async deleteCart(@Param('id') userId: string) {
    const cart = await this.cartService.deleteCart(userId);
    if (!cart) throw new NotFoundException('Cart does not exist');
    return cart;
  }
}

```

在上面的代码中，我们为三个方法使用了`@UseGuards`和`@Roles`装饰器。这指示应用程序，客户必须登录，并且必须有一个分配的`user`角色来添加或删除产品。

就是这样。如果你按照正确的步骤进行，你应该有一个基本但功能齐全的 NestJS eccomerce 应用程序。

## 结论

唷！这是一次相当长的旅程。我希望你喜欢 NestJS 并从中了解到一些新的东西。

尽管构建这个 NestJS 电子商务应用程序示例的每个步骤都需要详细的解释，但它非常基本，可以扩展到包括更多的功能。以下是一些你可以尝试的想法:

如您所见，NestJS 是一个强大而灵活的服务器端框架，可以为您的下一个项目提供一个健壮且可伸缩的结构。如果你想了解更多，深入到官方的 Nest 文档中，开始构建优秀的应用程序。

## [LogRocket](https://lp.logrocket.com/blg/ecommerce-signup) :看看用户为什么不完成你的电子商务流程中的一个步骤的技术和 UX 原因。

[![](img/d60d88871d85e76e0dcca90f4bbaf78c.png)](https://lp.logrocket.com/blg/ecommerce-signup)

LogRocket 就像是一个网络和移动应用程序和网站的 DVR，记录你的电子商务应用程序上发生的一切。LogRocket 没有猜测用户不转化的原因，而是主动揭示了阻止你转化的根本原因，比如 JavaScript 错误或死点击。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

开始主动监控您的电子商务应用程序— [免费试用](https://lp.logrocket.com/blg/ecommerce-signup)。