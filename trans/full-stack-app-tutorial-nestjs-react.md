# NestJS 和 React - LogRocket 博客的全栈应用教程

> 原文：<https://blog.logrocket.com/full-stack-app-tutorial-nestjs-react/>

NestJS 是一个健壮的框架，用于构建高效的、可伸缩的 Node.js 服务器端应用程序。Nest 提供了许多特性，允许开发人员使用他们选择的编程范式(函数式、面向对象或函数式反应)来构建 web 应用程序。Nest 还使用健壮的 Node.js 框架，如 [Express](https://expressjs.com) (默认)和 [Fastify](https://www.fastify.io) ，并内置了对 TypeScript 的支持，可以自由使用纯 JavaScript。

本教程将通过使用 NestJS 和 React 来构建一个全栈视频流应用程序，展示两者的结合。

为什么是视频流？流媒体是数据流最常见的使用案例之一。在视频应用的场景中，流媒体使用户能够立即观看视频，而无需首先下载视频。流式传输节省了用户的时间，并且不消耗存储空间。

流媒体也有利于应用程序的性能。使用这种类型的数据传输，数据是以小段或大块的形式发送的，而不是一次全部发送。这有利于 app 效率和成本管理。

在本文中，我们将深入探讨用 NestJS 构建应用后端，用 React 构建应用前端，然后部署全栈应用。

## 入门指南

本实践教程具备以下先决条件:

*   [Node.js](https://nodejs.org/) 版本> = 10.13.0 已安装，13 版本除外
*   MongoDB 数据库
*   Ubuntu 20.04，或者你选择的操作系统

## 构建 NestJS 后端

为了创建应用程序的后端，我们将遵循以下步骤:

### 安装和配置 NestJS

为了安装和配置一个新的 NestJS 项目，我们将使用 Nest 的命令行界面。

打开终端并运行以下命令:

```
npm i -g @nestjs/cli

```

安装完成后，创建一个项目文件夹:

```
mkdir VideoStreamApp && cd VideoStreamApp

```

接下来，通过运行以下命令创建新的 NestJS 项目:

```
nest new backend

```

当提示为项目选择包管理器时，选择 npm。

这将创建一个`backend`文件夹、节点模块和一些其他样板文件。还将创建一个`src`文件夹，并用几个核心文件填充。你可以在 [NestJS 官方文档](https://docs.nestjs.com/first-steps)中阅读更多关于这些文件的信息。

嵌套，让我们`cd`进入后端目录:

```
cd backend

```

### 安装依赖项

接下来，让我们安装这个项目需要的依赖项:

*   [mongose](https://mongoosejs.com):MongoDB 的基于 Node.js 的 ODM 库
*   [Multer](https://www.npmjs.com/package/multer) :处理文件上传的中间件
*   JSON web 令牌 (JWT):认证处理器
*   通用唯一 ID (UUID):随机文件名生成器

现在，运行以下代码:

```
npm i -D @types/multer @nestjs/mongoose mongoose @nestjs/jwt passport-jwt @types/bcrypt bcrypt @types/uuid @nestjs/serve-static

```

一旦依赖项的安装完成，我们将为项目设置一个嵌套服务器。

### 设置嵌套服务器

现在我们已经安装了依赖项，让我们通过在`src`目录中创建额外的文件夹来设置嵌套服务器。我们将在`src`目录中创建一个`model`、`controller`、和`utils`目录。

接下来，打开`src/main.ts`文件，并通过向 Boostrap 函数添加以下代码片段来启用 [CORS](https://www.npmjs.com/package/cors) 连接/快速 npm 包:

```
 app.enableCors();

```

### 设置 MongoDB 数据库

我们将使用[mongose](https://mongoosejs.com/docs/api.html)将应用程序连接到 MongoDB 数据库。

首先，我们将为应用程序设置一个 MongoDB 数据库。打开`/src/app.module.ts`文件，并添加以下代码片段:

```
...
import { MongooseModule } from '@nestjs/mongoose';
@Module({
  imports: [
     MongooseModule.forRoot('mongodb://localhost:27017/Stream'),
  ],
...

```

在这段代码中，我们将`MongooseModule`导入到根`AppModule`中，并使用`forRoot`方法来配置数据库。

### 定义模式

现在应用程序已经连接到 MongoDB 数据库，让我们定义应用程序将需要的数据库模式。打开`/src/model`文件夹，创建一个`user.schema.ts`文件，并添加以下代码片段:

```
import { Prop, Schema, SchemaFactory } from "@nestjs/mongoose";
export type UserDocument = User & Document;
@Schema()
export class User {
    @Prop({required:true})
    fullname: string;
    @Prop({required:true, unique:true, lowercase:true})
    email: string;
    @Prop({required:true})
    password: string
    @Prop({default: Date.now() })
    createdDate: Date
}
export const UserSchema = SchemaFactory.createForClass(User)

```

在这段代码中，我们从 Mongoose 导入了`@Prop()`、`@Schema()`、`@SchemaFactory()`装饰器。`@Prop()`装饰器将用于定义数据库集合的属性。`@Schema()`装饰器将为模式定义标记一个类，而`@SchemaFactory()`装饰器将生成模式。

我们还在 prop 装饰器中定义了一些有效性规则。我们希望所有字段都是必填的。我们指定`email`应该是唯一的，并转换成小写。我们还指定当前日期应该用作`createdDate`字段的默认日期。

接下来，让我们在`model`目录中创建一个`video.schema.ts`文件，并添加以下代码片段:

```
import { Prop, Schema, SchemaFactory } from "@nestjs/mongoose";
import * as mongoose from "mongoose";
import { User } from "./user.model";

export type VideoDocument = Video & Document;
@Schema()
export class Video {
    @Prop()
    title: string;
    @Prop()
    video: string;
    @Prop()
    coverImage: string;
    @Prop({ default: Date.now() })
    uploadDate: Date
    @Prop({ type: mongoose.Schema.Types.ObjectId, ref: "User" })
    createdBy: User
}
export const VideoSchema = SchemaFactory.createForClass(Video)

```

在这段代码中，我们导入了`mongoose`和`User`模式类。这将使我们能够引用和保存使用该应用程序创建视频的用户的详细信息。

### 定义应用程序路线

既然已经定义了模式，那么是时候定义应用程序的路由了。让我们从在`controllers`目录中创建一个`user.controller.ts`文件开始。

接下来，我们将导入用户路由所需的装饰器，导入`User`模式类、`UserService`类(我们将在本文稍后创建)和`JwtService`类来处理用户认证:

```
import { Body, Controller, Delete, Get, HttpStatus, Param, Post, UploadedFiles, Put, Req, Res } from "@nestjs/common";
import { User } from "../model/user.schema";
import { UserService } from "../model/user.service";
import { JwtService } from '@nestjs/jwt'
...

```

我们将使用`@Controller()`装饰器创建`Signup`和`Signin`路线，传递`api` URL。我们还将创建一个带有`constructor`函数的`UserController`类，在这里我们将为`userService`类和`JwtService`类创建变量。

```
@Controller('/api/v1/user')
export class UserController {
    constructor(private readonly userServerice: UserService,
        private jwtService: JwtService
    ) { }
...

```

现在，我们将使用`@Post`装饰器来创建`Signup`和`Signin`路线，两者都将监听`Post`请求:

```
@Post('/signup')
    async Signup(@Res() response, @Body() user: User) {
        const newUSer = await this.userServerice.signup(user);
        return response.status(HttpStatus.CREATED).json({
            newUSer
        })
    }
    @Post('/signin')
    async SignIn(@Res() response, @Body() user: User) {
        const token = await this.userServerice.signin(user, this.jwtService);
        return response.status(HttpStatus.OK).json(token)
    }
}

```

在这段代码中，我们使用`@Res()`装饰器向客户端发送响应，使用`@Body()`装饰器解析`Signup`路由的请求体中的数据。

我们通过将`user`模式对象发送给`userService`注册方法来创建一个新用户，然后使用内置的嵌套`HttpsStatus.CREATED`方法将新用户返回给带有 201 状态代码的客户端。

我们发送`user`模式对象和`jwtService`作为`Signin`路线的参数。然后，我们调用`userService`中的`Signin`方法对`user`进行认证，如果登录成功，返回一个`token`给客户端。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 创建用户验证

现在，我们将创建应用程序的安全性和用户身份管理。这包括用户与应用程序的所有初始交互，如登录、认证和密码保护。

首先打开`/src/app.module.ts`文件，将`jwtService`和`ServeStaticModule`导入到根`AppModule`中。`ServeStaticModule`装饰器使我们能够将文件呈现给客户机。

接下来，我们将在`utils`目录中创建`constants.ts`文件，并使用下面的代码片段导出 JWT `secret`:

```
export const secret = 's038-pwpppwpeok-dffMjfjriru44030423-edmmfvnvdmjrp4l4k';

```

在生产中，`secret`密钥应该安全地存储在。env 文件或放入专用的秘密管理器。应用程序模块应该类似于下面的代码片段:

```
...
import { ServeStaticModule } from '@nestjs/serve-static';
import { JwtModule } from '@nestjs/jwt';
import { secret } from './utils/constants';
import { join } from 'path/posix';

@Module({
  imports: [
    ....
    JwtModule.register({
      secret,
      signOptions: { expiresIn: '2h' },
    }),
    ServeStaticModule.forRoot({
      rootPath: join(__dirname, '..', 'public'),
    }),
   ...
  ],
...

```

接下来，我们将在服务文件夹中创建一个`user.service.ts`文件，并添加以下代码片段:

```
import { Injectable, HttpException, HttpStatus } from "@nestjs/common";
import { InjectModel } from "@nestjs/mongoose";
import { Model } from "mongoose";
import { User, UserDocument } from "../model/user.schema";
import * as bcrypt from 'bcrypt';
import { JwtService } from '@nestjs/jwt';
...

```

在这段代码中，我们导入了`Injectable`、`HttpException`、`HttpStatus`、`InJectModel`、`Model`、`bcrypt`和`JwtService`。`@Injectable()`装饰器附加了元数据，声明`UserService`是一个可以由嵌套反转控制(IoC)容器管理的类。`@HttpException()`装饰器将用于错误处理。

现在，我们将创建`UserService`类，并使用`@InjectModel`装饰器将模式注入到`constructor`函数中:

```
...
@Injectable()
export class UserService {
    constructor(@InjectModel(User.name) private userModel: Model<UserDocument>,
    ) { }
...

```

接下来，我们将创建一个`signup`函数，它将返回一个`user`作为承诺。我们将使用`bcrypt`对用户密码进行加盐和散列处理，以获得额外的安全性。我们将密码的散列版本保存到数据库中，并返回新创建的用户`newUser`。

```
...
async signup(user: User): Promise<User> {
        const salt = await bcrypt.genSalt();
        const hash = await bcrypt.hash(user.password, salt);
        const reqBody = {
            fullname: user.fullname,
            email: user.email,
            password: hash
        }
        const newUser = new this.userModel(reqBody);
        return newUser.save();
    }
...

```

下一步是创建一个允许用户登录应用程序的`signin`函数。

首先，我们将在`userModel`上运行一个查询，以确定用户记录是否已经存在于集合中。当找到一个用户时，我们将使用`bcrypt`来比较输入的密码和存储在数据库中的密码。如果密码匹配，我们将为用户提供一个访问令牌。如果密码不匹配，代码将抛出异常。

```
...
    async signin(user: User, jwt: JwtService): Promise<any> {
        const foundUser = await this.userModel.findOne({ email: user.email }).exec();
        if (foundUser) {
            const { password } = foundUser;
            if (bcrypt.compare(user.password, password)) {
                const payload = { email: user.email };
                return {
                    token: jwt.sign(payload),
                };
            }
            return new HttpException('Incorrect username or password', HttpStatus.UNAUTHORIZED)
        }
        return new HttpException('Incorrect username or password', HttpStatus.UNAUTHORIZED)
    }
...

```

接下来，我们创建一个`getOne`函数来基于一个`email`地址检索用户数据:

```
  async getOne(email): Promise<User> {
        return await this.userModel.findOne({ email }).exec();
    }

```

### 创建视频控制器

现在，我们将创建视频控制器。首先，我们需要配置 Multer 来允许视频的上传和流式传输。

打开`/src/app.module.ts`文件并添加以下代码片段:

```
...
import { MulterModule } from '@nestjs/platform-express';
import { diskStorage } from 'multer';
import { v4 as uuidv4 } from 'uuid';

@Module({
 imports: [
    MongooseModule.forRoot('mongodb://localhost:27017/Stream'),
    MulterModule.register({
      storage: diskStorage({
        destination: './public',
        filename: (req, file, cb) => {
          const ext = file.mimetype.split('/')[1];
          cb(null, `${uuidv4()}-${Date.now()}.${ext}`);
        },
      })
    }),
...

```

在这段代码中，我们将`MulterModule`导入到根`AppModule`中。我们从 Multer 导入`diskStorage`，提供将文件存储到磁盘的完全控制。我们还从`uuid`导入`v4`，为上传的文件生成随机名称。我们使用`MulterModule.register`方法将文件上传到磁盘的`/public`文件夹中。

接下来，我们在控制器目录中创建一个`video.conmtroller.ts`文件，并添加以下代码片段:

```
import { Body, Controller, Delete, Get, HttpStatus, Param, Post, UseInterceptors, UploadedFiles, Put, Req, Res, Query } from "@nestjs/common";
import { Video } from "../model/video.schema"
import { VideoService } from "../video.service";
import { FileFieldsInterceptor, FilesInterceptor } from "@nestjs/platform-express";
...

```

在这段代码中，我们导入了`UseInterceptors`、`UploadedFiles`、`Video`模式、`VideoService`类、`FileFieldsInterceptor`、`FilesInterceptor`以及视频路径所需的其他装饰器。

接下来，我们将使用`@Controller`装饰器创建视频控制器，并传入`api` URL。然后，我们将创建一个带有`constructor()`函数的`VideoController`类，其中我们将为`VideoSevice`类创建一个`private`变量。

```
@Controller('/api/v1/video')
export class VideoController {
    constructor(private readonly videoService: VideoService){}
...

```

现在，我们将使用`@UseInterceptors`装饰器来绑定`@FileFieldsInterceptor`装饰器，后者使用`@UploadedFiles()`装饰器从`request`中提取文件。

我们将把文件字段传递给`@FileFieldsInterceptor`装饰器。属性指定每个字段只需要一个文件。

所有的表单数据文件都将存储在`files`变量中。我们将创建一个`requestBody`变量并创建对象来保存表单数据值。

这个变量被传递给`VideoService`类保存视频的细节，而 Multer 将视频和`coverImage`保存到磁盘。一旦保存了记录，创建的视频对象将返回给客户端，并带有 201 状态代码。

接下来，我们将创建`Get`、`Put`、`Delete`路线来使用视频 ID 获取、更新和删除视频。

```
...   
    @Post()
    @UseInterceptors(FileFieldsInterceptor([
        { name: 'video', maxCount: 1 },
        { name: 'cover', maxCount: 1 },
    ]))
    async createBook(@Res() response, @Req() request, @Body() video: Video, @UploadedFiles() files: { video?: Express.Multer.File[], cover?: Express.Multer.File[] }) {
        const requestBody = { createdBy: request.user, title: video.title, video: files.video[0].filename, coverImage: files.cover[0].filename }
        const newVideo = await this.videoService.createVideo(requestBody);
        return response.status(HttpStatus.CREATED).json({
            newVideo
        })
    }
    @Get()
    async read(@Query() id): Promise<Object> {
        return await this.videoService.readVideo(id);
    }
    @Get('/:id')
    async stream(@Param('id') id, @Res() response, @Req() request) {
        return this.videoService.streamVideo(id, response, request);
    }
    @Put('/:id')
    async update(@Res() response, @Param('id') id, @Body() video: Video) {
        const updatedVideo = await this.videoService.update(id, video);
        return response.status(HttpStatus.OK).json(updatedVideo)
    }
    @Delete('/:id')
    async delete(@Res() response, @Param('id') id) {
        await this.videoService.delete(id);
        return response.status(HttpStatus.OK).json({
            user: null
        })
    }
}

```

### 创建视频服务

创建视频控制器后，让我们创建视频服务。我们将从在服务文件夹中创建一个`video.service.ts`文件开始。然后，我们将使用下面的代码片段导入必要的模块:

```
import {
    Injectable,
    NotFoundException,
    ServiceUnavailableException,
} from "@nestjs/common";
import { InjectModel } from "@nestjs/mongoose";
import { Model } from "mongoose";
import { Video, VideoDocument } from "../model/video.schema";
import { createReadStream, statSync } from 'fs';
import { join } from 'path';
import { Request, Response } from 'express';
...

```

在这段代码中，我们从`fs`模块中导入了`createReadStream`和`statSync`。我们使用`createReadStream`来读取文件系统中的文件，使用`statSync`来获取文件的详细信息。然后，我们导入`Video`模型和`VideoDocument`。

现在，我们将创建我们的`VideoService`类，并使用`@InjectModel`装饰器将模式注入到`constructor`函数中:

```
...
@Injectable()
export class VideoService {
    constructor(@InjectModel(Video.name) private videoModel: Model<VideoDocument>) { }
...

```

接下来，我们将使用`createVideo`函数将视频细节保存到数据库集合中，并返回创建的`newVideo.save`对象:

```
...
    async createVideo(video: Object): Promise<Video> {
        const newVideo = new this.videoModel(video);
        return newVideo.save();
    }
...

```

然后，我们将创建`readVideo`函数，根据请求参数中的`id`获取视频细节。我们将`populate`创建视频的用户的名字，并将这个名字`createdBy`返回给客户端。

```
...
   async readVideo(id): Promise<any> {
        if (id.id) {
            return this.videoModel.findOne({ _id: id.id }).populate("createdBy").exec();
        }
        return this.videoModel.find().populate("createdBy").exec();
    }
...

```

接下来，我们将创建`streamVideo`函数，将视频作为流发送给客户端。我们将根据`id`查询数据库以获得视频的详细信息。如果找到了视频`id`，我们就从请求头中得到初始范围值。然后我们将使用视频细节从文件系统中获取视频。我们将把视频分成`1mb`个块并发送给客户端。如果没有找到视频`id`，代码将抛出一个`NotFoundException`错误。

```
...
   async streamVideo(id: string, response: Response, request: Request) {
        try {
            const data = await this.videoModel.findOne({ _id: id })
            if (!data) {
                throw new NotFoundException(null, 'VideoNotFound')
            }
            const { range } = request.headers;
            if (range) {
                const { video } = data;
                const videoPath = statSync(join(process.cwd(), `./public/${video}`))
                const CHUNK_SIZE = 1 * 1e6;
                const start = Number(range.replace(/\D/g, ''));
                const end = Math.min(start + CHUNK_SIZE, videoPath.size - 1);
                const videoLength = end - start + 1;
                response.status(206)
                response.header({
                    'Content-Range': `bytes ${start}-${end}/${videoPath.size}`,
                    'Accept-Ranges': 'bytes',
                    'Content-length': videoLength,
                    'Content-Type': 'video/mp4',
                })
                const vidoeStream = createReadStream(join(process.cwd(), `./public/${video}`), { start, end });
                vidoeStream.pipe(response);
            } else {
                throw new NotFoundException(null, 'range not found')
            }

        } catch (e) {
            console.error(e)
            throw new ServiceUnavailableException()
        }
    }
...

```

接下来，我们将创建`update`和`delete`函数来更新或删除数据库集合中的视频:

```
...
    async update(id, video: Video): Promise<Video> {
        return await this.videoModel.findByIdAndUpdate(id, video, { new: true })
    }
    async delete(id): Promise<any> {
        return await this.videoModel.findByIdAndRemove(id);
    }
}

```

尽管定义了控制器和服务，Nest 仍然不知道它们的存在，因此不会创建这些类的实例。

为了解决这个问题，我们必须将控制器添加到`app.module.ts file`，并将服务添加到`providers:`列表。然后，我们将导出`AppModule`中的模式和模型，并注册`ServeStaticModule`。这使我们能够将文件呈现给客户端。

```
....
import { ServeStaticModule } from '@nestjs/serve-static';
import { VideoController } from './controller/video.controller';
import { VideoService } from './service/video.service';
import { UserService } from './service/user.service';
import { UserController } from './controller/user.controller';
import { Video, VideoSchema } from './model/video.schema';
import { User, UserSchema } from './model/user.schema';

@Module({
  imports: [
    ....
    MongooseModule.forFeature([{ name: User.name, schema: UserSchema }]),
    MongooseModule.forFeature([{ name: Video.name, schema: VideoSchema }]),
    ....
    ServeStaticModule.forRoot({
      rootPath: join(__dirname, '..', 'public'),
    }),
  ],
  controllers: [AppController, VideoController, UserController],
  providers: [AppService, VideoService, UserService],
})

```

### 创建中间件

此时，Nest 现在知道应用程序中的控制器和服务是存在的。下一步是创建中间件来保护视频路由不被未经身份验证的用户访问。

首先，让我们在`/src`文件夹中创建一个`app.middleware.ts`文件，并添加以下代码片段:

```
import { JwtService } from '@nestjs/jwt';
import { Injectable, NestMiddleware, HttpException, HttpStatus } from '@nestjs/common';
import { Request, Response, NextFunction } from 'express';
import { UserService } from './service/user.service';
interface UserRequest extends Request {
    user: any
}
@Injectable()
export class isAuthenticated implements NestMiddleware {
    constructor(private readonly jwt: JwtService, private readonly userService: UserService) { }
    async use(req: UserRequest, res: Response, next: NextFunction) {
        try{

            if (
                req.headers.authorization &&
                req.headers.authorization.startsWith('Bearer')
            ) {
                const token = req.headers.authorization.split(' ')[1];
                const decoded = await this.jwt.verify(token);
                const user = await this.userService.getOne(decoded.email)
                if (user) {
                    req.user = user
                    next()
                } else {
                    throw new HttpException('Unauthorized', HttpStatus.UNAUTHORIZED)

                }
            } else {
                throw new HttpException('No token found', HttpStatus.NOT_FOUND)

            }
        }catch {
         throw new HttpException('Unauthorized', HttpStatus.UNAUTHORIZED)
       }
    }
}

```

在这段代码中，我们创建了一个`isAuthenticated`类，它实现了`NestMiddleware`。我们在请求头中从客户端获取令牌并验证令牌。如果令牌有效，则用户被授权访问视频路由。如果令牌无效，我们将引发一个`HttpException`。

接下来，我们将打开`app.module.ts`文件并配置中间件。我们将排除流路由，因为我们是从前端的视频元素流目录:

```
import { Module, RequestMethod, MiddlewareConsumer } from '@nestjs/common';
export class AppModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(isAuthenticated)
      .exclude(
        { path: 'api/v1/video/:id', method: RequestMethod.GET }
      )
      .forRoutes(VideoController);
  }
}

```

现在，让我们运行以下命令来启动 NestJS 服务器:

```
npm run start:dev

```

## 构建 React 应用前端

为了简化这部分教程，我为应用前端的 UI 创建了一个 [GitHub repo](https://github.com/Claradev32/VideoStreamApp/tree/dev) 。首先，克隆到`dev`分支，让我们把重点放在使用 API 和应用程序逻辑上。

为了设置视频流 React 应用程序的前端，我们将构建以下功能:

### 创建登录

UI 启动并运行后，让我们来处理用户登录应用程序的逻辑。打开`Component/Auth/Signin.js`文件，导入`axios`和`useNavigation`:

```
...
import axios from 'axios';
import { useNavigate } from "react-router-dom"
...

```

在这段代码中，我们使用`axios`向后端发出 API 请求。`useNavigation`用于成功登录后重定向用户。

现在，让我们用下面的代码片段创建一个`handleSubmit`处理函数:

```
...
export default function SignIn({setIsLoggedIn}) {
  const [errrorMessage, setErrorMessage] = React.useState('')
  let navigate = useNavigate();

  const handleSubmit = async (event) => {
    event.preventDefault();
    const formData = new FormData(event.currentTarget);
    const form = {
      email: formData.get('email'),
      password: formData.get('password')
    };
    const { data } = await axios.post("http://localhost:3002/api/v1/user/signin", form);
    if (data.status === parseInt('401')) {
      setErrorMessage(data.response)
    } else {
      localStorage.setItem('token', data.token);
      setIsLoggedIn(true)
      navigate('/video')
    }
  };
...

```

在这段代码中，我们从`props`中析构了`setIsLoggedIn`，创建了一个`errorMessage`状态，在用户登录时向用户显示错误消息。然后，我们使用`formData` API 从文本字段中获取用户`Formdata`，并使用`axios`向后端发送一个`.post`请求。

我们检查响应`status`以查看登录是否成功。成功登录后，我们将发送给用户的令牌保存在浏览器的`localStorage`中，将`setIsLoggedIn`状态重置为 true，并将用户重定向到视频页面。不成功的登录将导致`401(Unauthorized)`响应。在这种情况下，我们将向用户显示错误消息。

接下来，我们将向`form`组件添加一个`onSubmit`事件，并绑定`handleSubmit`处理程序。

```
...
<Box component="form" onSubmit={handleSubmit} noValidate sx={{ mt: 1 }}>
...

```

如果有一个`errorMessage`，我们将显示给用户:

```
<Typography component="p" variant="p" color="red">
  {errrorMessage}
</Typography>

```

### 创建用户帐户

现在，我们准备让用户登录应用程序。让我们创建一个允许用户创建帐户的`Signup`组件。打开`Component/Auth/Signup.js`，导入`axios`和`useNavigate`:

```
...
import { useNavigate } from 'react-router-dom';
import axios from 'axios';
...

```

接下来，我们将使用下面的代码片段创建一个`handleSubmit`处理函数:

```
...
export default function SignUp() {
    let navigate = useNavigate();
  const handleSubmit = async (event) => {
    event.preventDefault();
    const data = new FormData(event.currentTarget);
    const form = {
      fullname : data.get('fname') +' '+ data.get('lname'),
      email: data.get('email'),
      password: data.get('password')
    };
    await axios.post("http://localhost:3002/api/v1/user/signup", form); 
    navigate('/')
  };
...

```

在这段代码中，我们从`props`中析构了`setIsLoggedIn`，并创建了一个`errorMessage`状态，以便在用户登录时向其显示错误消息。然后，我们使用`formData` API 从表单文本字段获取用户输入数据，并使用`axios`向后端发送 post 请求。登录后，我们将用户重定向到登录页面。

接下来，我们将向 for 组件添加一个`onSubmit`事件，并绑定我们刚刚创建的`handleSubmit`处理程序。

```
Box component="form" noValidate onSubmit={handleSubmit} sx={{ mt: 3 }}>

```

### 将视频添加到库中

既然已经创建了用户身份验证组件，让我们让用户能够向库中添加视频。

我们将从打开`Component/Navbar/Header.js`开始，并导入`axios`:

```
...
import axios from 'axios';
...

```

接下来，我们将从属性中析构`isLoggedIn`状态，并为`video`、`cover`图像和`title`创建三个`React.useState`变量。

```
...
const [videos, setVideos] = React.useState("");
const [cover, setCover] = React.useState("");
const [title, setTitle] = React.useState("")
...

```

现在，我们将创建一个`submitForm`处理函数。在我们的`submitForm`函数中，我们将阻止表单的默认重载，并且我们将使用`formData` API 获取表单提交信息。为了授权用户访问视频端点，我们将从浏览器的本地存储中获取用户的令牌，然后[用`axios`](https://blog.logrocket.com/how-to-make-http-requests-like-a-pro-with-axios/) 发送一个`.post` HTTP 请求。

```
...  
   const submitForm = async (e) => {
        e.preventDefault();
        const formData = new FormData();
        formData.append("title", title);
        formData.append("video", video);
        formData.append("cover", cover);
        const token = localStorage.getItem('token');
        await axios.post("http://localhost:3002/api/v1/video", formData, {
            headers: ({
                Authorization: 'Bearer ' + token
            })
        })
    }
...

```

接下来，我们将把`submitForm`处理程序绑定到一个`onSubmit`事件，并将输入状态设置变量绑定到一个`onChange`事件。表单组件应该如下所示:

```
 <Box sx={style}>
    <Typography id="modal-modal-title" variant="h6" component="h2">
    <Box component="form" onSubmit={submitForm} noValidate sx={{ mt: 1 }}>
        <label>Video Title:</label>
        <TextField
           margin="normal"
           required
           fullWidth
           id="title"
           name="title"
           autoFocus
           onChange={(e) => setTitle(e.target.value)}
                                                />
     <label>Select Video:</label>
     <TextField
        margin="normal"
        required
        fullWidth
        id="video"
        name="video"
        autoFocus
        type="file"
        onChange={(e) => setVideos(e.target.files[0])}
    />
    <label>Select Cover Image:</label>
    <TextField
       autoFocus
       margin="normal"
       required
       fullWidth
       name="coverImage"
       type="file"
       id="coverImage"
       onChange={(e) => setCover(e.target.files[0])}
    />
   <Button
      type="submit"
      fullWidth
      variant="contained"
      sx={{ mt: 3, mb: 2 }}
    >
    Upload
   </Button>
</Box>

```

### 显示视频列表

让我们创建一个`VideoList`组件来向用户显示视频。打开`Component/Video/VideoList.js`文件，导入`axios`、`useParams`、`useEffect`和`useNavigate`。

```
...
import { Link, useNavigate } from 'react-router-dom'
import axios from 'axios';
...

```

接下来，我们将创建一个`videos`状态来存储视频，并创建一个`navigate`对象来在用户令牌过期时将用户重定向到登录页面:

```
...
    const [videos, setVideos] = React.useState([])
    const navigate = useNavigate();
...

```

当组件挂载时，我们将使用`React.useState`向 API 发送一个 get 请求。我们将从`localStorage`和[获得用户的`token`，并使用`axios`在请求头中将它发送给 API](https://blog.logrocket.com/using-axios-set-request-headers/) :

```
... 
React.useEffect(() => {
        async function fetchData() {
            try {
                const token = localStorage.getItem('token');
                const {data} = await axios.get('http://localhost:3002/api/v1/video', {
                    headers: ({
                        Authorization: 'Bearer ' + token
                    })
                });
                setVideos(data)
            } catch {
                setLoggedIn(false);
                navigate('/')
            }
        }
        fetchData();
    }, [navigate, setLoggedIn]);
...

```

接下来，我们将遍历处于`videos`状态的视频列表，并向用户显示该列表。我们将使用链接`component`创建一个到视频流页面的链接，解析 URL 中的视频。

```
...
{videos.map((video) => {
    return <Grid item xs={12} md={4} key={video._id}>
        <CardActionArea component="a" href="#">
            <Card sx={{ display: 'flex' }}>
                <CardContent sx={{ flex: 1 }}>
                    <Typography component="h2" variant="h5">
                        <Link to={`/video/${video._id}`} style={{ textDecoration: "none", color: "black" }}>{video.title}</Link>
                    </Typography>
                    <Typography variant="subtitle1" color="text.secondary">
                        {video.uploadDate}
                    </Typography>
                </CardContent>
                <CardMedia
                    component="img"
                    sx={{ width: 160, display: { xs: 'none', sm: 'block' } }}
                    image={`http://127.0.0.1:3002/${video.coverImage}`}
                    alt="alt"
                />
            </Card>
        </CardActionArea>
    </Grid>
})}
...

```

### 视频流

现在，让我们创建一个组件来流式传输用户选择的任何视频。打开`Componenet/Video/Video.js`文件，导入`useNavigation`和`useParams`和`axios`。我们将使用`useNavigation`和`useParams`来获取用户想要传输的视频的`id`。

```
import { useParams, useNavigate } from 'react-router-dom';
import axios from 'axios';

```

我们将发送一个带有`axios`的`GET`请求，在 URL 参数中带有`videoId`，在请求头中带有用户的`token`以进行授权。

如果令牌无效，我们将重置`isLoggedIn`状态并将用户重定向到登录页面。

```
React.useEffect(() => {
        async function fetchData() {
            try {
                const token = localStorage.getItem('token');
                const {data} = await axios.get(`http://127.0.0.1:3002/api/v1/video?id=${videoId}`, {
                    headers: ({
                        Authorization: 'Bearer ' + token
                    })
                });
                setVideoInfo(data)
            } catch {
                setLoggedIn(false);
                navigate('/')
            }
        }
        fetchData();
}, [videoId, navigate, setLoggedIn]);

```

现在，我们将向用户显示视频的详细信息，并解析 video 元素中的视频 URL 来传输视频:

```
<Container>
    <Grid item xs={12} md={12} marginTop={2}>
        <CardActionArea component="a" href="#">
            <Card sx={{ display: 'flex' }}>
                <CardContent sx={{ flex: 1 }}>
                    <video autoPlay controls width='200'>
                        <source src={`http://localhost:3002/api/v1/video/${videoId}`} type='video/mp4' />
                    </video>
                </CardContent>
            </Card>
        </CardActionArea>
    </Grid>
    <Grid container spacing={2} marginTop={2}>
        <Grid item xs={12} md={6}>
            <Typography variant="subtitle1" color="primary">
                Created by:{videoInfo.createdBy?.fullname}
            </Typography>
        </Grid>
        <Grid item xs={12} md={6}>
            <Typography variant="subtitle1" color="primary">
                Created: {videoInfo.uploadDate}
            </Typography>
        </Grid>
        <Grid item xs={12} md={12}>
            <Typography variant="h5">
                {videoInfo.title}
            </Typography>
        </Grid>
    </Grid>
</Container>

```

### 部署应用程序

现在，确保我们在`frontend`目录中，让我们运行下面的命令来部署应用程序:

```
npm start 

```

## 结论

在本教程中，我们介绍了 NestJS 作为构建可伸缩 Node.js 应用程序的框架。我们通过使用 NestJS 和 React 构建一个全栈视频流应用程序来演示这个概念。本教程中共享的代码可以通过向 UI 添加更多样式以及添加更多组件来扩展。

本文中使用的完整项目代码可以在 [GitHub](https://github.com/Claradev32/VideoStreamApp/tree/master) 上获得。请随意在 Heroku 上部署此应用程序，并与朋友分享。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)