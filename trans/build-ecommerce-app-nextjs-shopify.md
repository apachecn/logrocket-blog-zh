# 用 Next.js 和 shotify-log 火箭博客构建一个电子商务应用程序

> 原文：<https://blog.logrocket.com/build-ecommerce-app-nextjs-shopify/>

### 目录

自从新冠肺炎出现在世界上，电子商务已经爆炸。根据国际贸易管理局的数据，2020 年 COVID 电子商务前后的收入增长了 19%。因此，企业家转向在线平台，如 Shopify、WordPress(及其插件 WooCommerce)和 Squarespace，以建立他们的在线商店。

使用 Shopify，用户不仅可以建立一个在线商店，而且由于他们的[店面 API](https://shopify.dev/api/storefront) ，开发人员可以创建一个定制的客户端应用程序来连接它。

在本文中，您将学习如何建立一个 Shopify 商店并获得您的店面 API 令牌。获得令牌后，您将创建一个 Next.js 应用程序，用虚拟数据列出并显示您的产品。最后，将 Next.js 应用程序连接到 Shopify 应用程序，以获取真正的产品。

## 创建 Shopify 商店(可选)

如果你已经建立了一个 Shopify 商店，你可以跳过这一步，直接进入第二步。

如果没有，去 Shopify 的网站注册免费试用。完成后，您可以开始创建一些集合，用于对您的产品进行分类。

要创建系列，请转到**产品**，然后转到**系列**。接下来，点击**创建收藏**。输入标题并选择**手动**作为收藏类型。对于后者，您可以手动将产品添加到该集合中。

![Create Collection](img/8438cff729a317184d726e535032594a.png)

对于本教程，两个集合是必要的:男人和女人。

一旦你的收藏设置完毕，点击**产品**，然后**添加产品**。

要创建产品，请遵循以下步骤:

1.  输入一个**标题**
2.  上传图片在**媒体**文件中输入
3.  选择一种**类型**(即鞋子)
4.  选择一个**系列**(即女性或男性)
5.  输入一个**价格**
6.  在**库存**部分，在**可用**栏增加一个**数量**
7.  将**产品状态**设置为**激活**
8.  最后点击**保存**。这将为你的商店创造一个活跃的产品。重复这些步骤几次，以填补你的商店。例如，这里有一个网上商店，里面有一些鞋子:

![Online Store Shoes](img/0e7bd436d2e0ba089f1d5fc89827dc8d.png)

## 获取 Shopify 的店面 API 令牌

设置好商店后，您将需要一个 API 令牌从第三方应用程序访问它。转到**应用**，点击**为你的商店开发应用**，并按照步骤启用应用开发。

![App Development](img/5f7b7cb9dfed655ccf065a07c80fe7ae.png)

启用此功能后，点击**创建应用**并填写您的应用名称。

一旦你的应用程序创建完成，点击你的新应用程序，进入**配置**。在店面 API 部分，点击**配置**。

![Configuration Tab](img/76f9510788ee13c0e0a5dcbea71105cd.png)

接下来，在 **Storefront API 访问范围**下，选择所有复选框。这将使您能够在未经身份验证的情况下从您的商店获取数据，例如您的产品。点击**保存**。

![Select Checkboxes](img/1d67a018462731b116fb8444cb5e47a0.png)

要获得您的店面访问令牌，您需要安装您新创建的应用程序，因此单击绿色的 **Install** 按钮。

现在，您可以通过返回到您的应用程序和 **API 凭证**来检索您的访问令牌。请记下该令牌，因为您稍后会用到它。

![API Credentials](img/2ea984057d803d3842b6c361414ecc2c.png)

## 设置 Next.js 应用程序

对于本教程，您将使用 [MUI](https://mui.com/) 。这个组件库允许你获得预构建的组件来更快地构建 UI。

幸运的是，MUI 还附带了一个 Next.js starter 项目，您可以通过运行以下命令获得它:

```
  curl https://codeload.github.com/mui/material-ui/tar.gz/master | tar -xz --strip=2  material-ui-master/examples/nextjs
```

不幸的是，前面的命令将创建默认名称为`nextjs`的项目。如果您想重命名它，请运行:

```
    mv nextjs <your_app_name> # Mac 
    ren nextjs <your_app_name> # Windows

```

完成后，进入您的项目并安装依赖项:

```
    cd <your_app_name>
    npm install
    npm run dev

```

用一些虚拟数据创建你的用户界面会更容易。为此，在您的根文件夹中创建一个`data.js`,并添加一些虚拟产品:

```
    const PRODUCTS = [
      {
        handle: 1,
        name: 'Women Black Shoes',
        price: '14.99',
        collection: 'women',
        image: 'https://external-content.duckduckgo.com/iu/?u=http%3A%2F%2Fcohenwoodworking.com%2Fwp-content%2Fuploads%2F2016%2F09%2Fimage-placeholder-500x500.jpg&f=1&nofb=1'
      },
      {
        handle: 2,
        name: 'Women Brown Shoes',
        price: '14.99',
        collection: 'women',
        image: 'https://external-content.duckduckgo.com/iu/?u=http%3A%2F%2Fcohenwoodworking.com%2Fwp-content%2Fuploads%2F2016%2F09%2Fimage-placeholder-500x500.jpg&f=1&nofb=1'
      },
      {
        handle: 3,
        name: 'Women Purple Shoes',
        price: '14.99',
        collection: 'women',
        image: 'https://external-content.duckduckgo.com/iu/?u=http%3A%2F%2Fcohenwoodworking.com%2Fwp-content%2Fuploads%2F2016%2F09%2Fimage-placeholder-500x500.jpg&f=1&nofb=1'
      }
    ];

    export default PRODUCTS;

```

## 用 Next.js 创建电子商务商店的主页

在 Next.js 根文件夹中，创建一个`components`文件夹。

在这个文件夹中，您将创建一个名为`ProductList`的组件来列出您的产品。MUI 有一些预构建的组件，以网格格式创建一个带有图像和标题的列表。

这些组件被称为:`ImageList`、`ImageListItem`和`ImageListItemBar`。你可以在这里找到文档[。](https://mui.com/material-ui/react-image-list/#image-list-with-title-bars)

在你的`components`文件夹中，创建`ProductsList.js`。从 MUI 导入所有必要的组件，并设置您的网格。假设您的组件将接收一个产品数组，并映射到它上面来创建一个产品条目，下面有一个图像和一个标题。

```
 import * as React from 'react';
    import Typography from '@mui/material/Typography';
    import ImageList from '@mui/material/ImageList';
    import ImageListItem from '@mui/material/ImageListItem';
    import ImageListItemBar from '@mui/material/ImageListItemBar';

    export default function ProductsList({products}) {
      return (
        <ImageList cols={5} gap={20}>
          {products.map((product) => (
            <ImageListItem key={product.image}>
              {/* Product's image*/}
              <img
                src={`${product.image}?w=250&fit=crop&auto=format`}
                srcSet={`${product.image}?w=250&fit=crop&auto=format&dpr=2 2x`}
                alt={product.name}
                loading="lazy"
              />
              {/* Product's name + price under the image */}
              <ImageListItemBar
                title={product.name}
                subtitle={<span>Price: {product.price}</span>}
                position="below"
              />
            </ImageListItem>
          ))}
        </ImageList>
      )
    };

```

您需要的另一个组件是导航栏。你可以使用 MUI 的`AppBar`和`Toolbar`来创建一个。在里面，将您的收藏添加为文本。稍后您将学习如何设置路由。

这是您的`Navigation.js`组件的‌result:

```
  import * as React from 'react';
    import AppBar from '@mui/material/AppBar';
    import Toolbar from '@mui/material/Toolbar';
    import Typography from '@mui/material/Typography';

    export default function Navigation() {
      return (
        <AppBar position="static">
          <Toolbar>
            <Typography mr={2}>All products</Typography>
            <Typography mr={2}>Women</Typography>
            <Typography>Men</Typography>
          </Toolbar>
        </AppBar>
      )
    };

```

最后，在应用程序的主页中使用新创建的组件`ProductsList`和`Navigation.js`。这个页面可以在 URL 上找到，并且是从`pages/index.js`文件中呈现的。

*注意:如果你不熟悉 Next.js 的页面功能及其渲染，‌check 调出* [文档](https://nextjs.org/docs/basic-features/pages) *。*

在 `index.js, import Navigation, ProductLists`里面，还有你的虚拟数据。您可以将数据传递到产品列表中:

```
  import * as React from 'react';
    import Container from '@mui/material/Container';
    import Box from '@mui/material/Box';

    import Navigation from '../components/Navigation';
    import ProductsList from '../components/ProductsList';
    import PRODUCTS from '../data.js';

    export default function Index() {
      return (
        <Box>
          <Navigation />
          <Container maxWidth="lg">
            <ProductsList products={PRODUCTS} />
          </Container>
        </Box>
      );
    }

```

至此，刷新页面后，您应该会看到:

![Shoes Placeholders](img/52cfef4d837cfd947cc3b31e7e09943c.png)

## 在电子商务应用程序中创建不同的收藏页面

现在你的主页已经设置好了，你需要收藏页面。大多数网上商店将他们的产品分类，这样用户更容易浏览他们的目录。因此，我们将创建一个特定的集合。

Next.js 提供动态路由。如果你对它不熟悉，它允许你创建路线，比如`/collections/[collectionName`。通过这样做，像`/collections/women`或`/collections/men`这样的 URL 将使用相同的`.js`页面。

在你的`pages`文件夹中，创建一个`collections`文件夹，并在其中创建一个`[collectionName].js`文件。这个组件将非常类似于你的主页。但是，您将过滤您的产品，只从正确的集合中获取产品。

幸运的是，Next.js 还附带了一个`useRouter`钩子，通过它可以从 URL 获取查询参数。一旦你有了`collectionName`，你就可以在过滤你的产品时使用它。最后，把你过滤后的产品清单交给`ProductsList`。

```
 import * as React from 'react';
    import { useRouter } from 'next/router'

    import Container from '@mui/material/Container';
    import Box from '@mui/material/Box';

    import Navigation from '../../components/Navigation';
    import BreadcrumbsNavigation from '../../components/BreadcrumbsNavigation';
    import ProductsList from '../../components/ProductsList';
    import PRODUCTS from '../../data.js';

    export default function CollectionPage() {
      const router = useRouter()
      const { collectionName } = router.query
      const products = PRODUCTS.filter(product => product.collection === collectionName)
      return (
        <Box>
          <Navigation />
          <Container maxWidth="lg">
            <BreadcrumbsNavigation collection={collectionName} />
            <ProductsList products={products} />
          </Container>
        </Box>
      );
    }
```

请注意，您的收藏页面使用了一个名为`BreadcrumbsNavigation`的定制组件。这是为你的商店渲染自定义面包屑。

要创建这个组件，在您的`components`文件夹中，创建一个`BreadcrumbsNavigation.js`。MIU 提供了一个`Breadcrumbs`组件供你使用。除此之外，`Link`组件允许您添加返回主页的路径。

以下是最终结果:

```
    import * as React from 'react';

    import Box from '@mui/material/Box';
    import Breadcrumbs from '@mui/material/Breadcrumbs';
    import Typography from '@mui/material/Typography';
    import Link from '@mui/material/Link';

    export default function BreadcrumbsNavigation({title}) {
      return (
        <Box mt={2}>
          <Breadcrumbs separator="›" aria-label="breadcrumb">
            <Link underline="hover" key="1" color="inherit" href="/">
              Products
            </Link>
            <Typography key="3" color="text.primary">
              {title && title.replace(/^\w/, c => c.toUpperCase())}
            </Typography>
          </Breadcrumbs>
        </Box>
      )
    }

```

在`Navigation.js`中，导入您刚刚使用的 MUI 的`Link`组件，并在您的应用程序栏上为您的主页和收藏添加一些路由:

```
    import * as React from 'react';

    import AppBar from '@mui/material/AppBar';
    import Toolbar from '@mui/material/Toolbar';
    import Typography from '@mui/material/Typography';
    import Link from '@mui/material/Link';

    export default function Navigation() {
      return (
        <AppBar position="static">
          <Toolbar>
            <Link href="/" underline="none" color="inherit"><Typography mr={2}>All products</Typography></Link>
            <Link href="/collections/women" underline="none" color="inherit"><Typography mr={2}>Women</Typography></Link>
            <Link href="/collections/men" underline="none" color="inherit"><Typography>Men</Typography></Link>
          </Toolbar>
        </AppBar>
      )
    };

```

在您的 `ProductList.js`中，如果没有产品，您可以添加检查:

```
    import * as React from 'react';

    import Box from '@mui/material/Box';
    import Typography from '@mui/material/Typography';
    ...

    export default function ProductsList({products}) {
      return (
        <Box>
          {
            (products && products.length > 0) ?
            <ImageList cols={5} gap={20}>
              ...
            </ImageList>:
            <Typography variant="body1" align="center">There are no products in this collection</Typography>
          }
        </Box>
      )
    };

```

如果您前往`[http://localhost:3000/collections/women](http://localhost:3000/collections/women)`，您现在应该会看到:

![Shoes Icons](img/585ede68cda0c70fd0c3d753356c5505.png)

## 在 Next.js 和 Shopify 中创建一个产品页面

设置好主页和收藏页面后，您可以进入产品页面。与集合页面一样，Next.js 动态路由可用于设置页面。

创建一个`products`文件夹。在里面，添加一个名为`[productHandle].js`的新文件。无论你是想使用 id 还是像`black-converse`一样的句柄作为`productHandle`，这个页面都会‌渲染你的产品页面。

从 URL 获取`productHandle`参数后，就可以用它来抓取你的产品了。为了显示产品的图像，Next.js 提供了一个内置图像优化的`[Image](https://nextjs.org/docs/basic-features/image-optimization)`组件。

以下是产品页面的‌result:

```
  import * as React from 'react';
    import { useRouter } from 'next/router';
    import Image from 'next/image';

    import Container from '@mui/material/Container';
    import Box from '@mui/material/Box';
    import Grid from '@mui/material/Grid'
    import Typography from '@mui/material/Typography';
    import Button from '@mui/material/Button';

    import Navigation from '../../components/Navigation';
    import BreadcrumbsNavigation from '../../components/BreadcrumbsNavigation';
    import ProductsList from '../../components/ProductsList';
    import PRODUCTS from '../../data.js';

    export default function ProductPage() {
      const router = useRouter()
      // Get productHandle from url: /products/[productHandle]
      const { productHandle } = router.query
      // Get product data
      const product = PRODUCTS.find(product => product.handle === parseInt(productHandle))
      const { name, image, price } = product || {}

      return (
        <Box>
          <Navigation />
          {product &&
            <Container maxWidth="lg">
              <BreadcrumbsNavigation title={name} />
                <Grid container direction="row">
                  <Grid item xs={6}>
                    <Image
                      src={image}
                      alt={`Picture of ${name}`}
                      width={500}
                      height={500}
                    />
                  </Grid>
                  <Grid item xs={6}>
                    <Typography variant="h3" my={2}>{name}</Typography>
                    <Grid mt={4}>
                      <Typography variant="h6" component="span">Price: </Typography>
                      <Typography variant="body1" component="span">{price}</Typography>
                    </Grid>
                    <Grid mt={1}>
                      <Button variant="contained">Add to cart</Button>
                    </Grid>
                  </Grid>
                </Grid>
            </Container>
          }
        </Box>
      );
    }

```

如果您最终使用了`Image`组件，您需要将您的图像的域添加到`next.config.js`，如下所示:

```
  module.exports = {
      reactStrictMode: true,
      images: {
        domains: ['external-content.duckduckgo.com'],
      },
    };

```

更改`next.config.js`时，您必须重启服务器。

最后，让我们实现到产品页面的路由。为此，当用户点击产品列表中的图片时，回到您的`components/ProductsList.js`并使用 Next.js 路由器导航到您的产品页面。在您的`ImageListItem`上添加`onClick`，以及光标样式，并创建一个`goToProductPage`来导航。

以下是最终结果:

```
import * as React from 'react';
    import { useRouter } from 'next/router'

    ...

    export default function ProductsList({products}) {
      const router = useRouter()
      // Navigate to product's page
      const goToProductPage = productHandle => router.push(`/products/${productHandle}`)

      return (
        <Box>
          {
            (products && products.length > 0) ?
            <ImageList cols={5} gap={20}>
              {products.map((product) => (
                <ImageListItem
                  key={product.image}
                  style={{cursor: 'pointer'}}
                  onClick={() => goToProductPage(product.handle)}>
                  ...
                </ImageListItem>
              ))}
            </ImageList>:
            <Typography variant="body1" align="center">There are no products in this collection</Typography>
          }
        </Box>
      )
    };

```

渲染后，您将得到以下内容:

![Women Black Shoes](img/302d79459f7a20d897729df2d94f2c88.png)

## 使用 Shopify Storefront API 检索您的产品

您的客户端应用程序现在已经设置好了。然而，它依赖于虚拟数据。为了完成你的项目，需要从你的 Shopify 商店获取数据并使用。

为此，您将需要 [Shopify JavaScript Buy SDK](https://github.com/Shopify/js-buy-sdk) ，您可以通过安装必要的软件包来获得它:

```
    npm install shopify-buy

```

还记得我们在本文开头得到的店面 API 令牌吗？您将把它们存储在环境变量中。在您的根项目中，创建一个名为`.env.local`的文件，并添加您的令牌以及您的 Shopify 域:

```
  SHOPIFY_STORE_FRONT_ACCESS_TOKEN=*****
    SHOPIFY_STORE_DOMAIN=********

```

您也可以点击 Shopify 商店中的**设置**来查找您的域名:

![Settings Shopify](img/4e34788c2bf89c4b53162f0c56e201f8.png)

一旦设置好变量，在根项目中创建一个`lib`文件夹，并在其中创建一个`shopify.js`文件。在这个文件中，您将导入您的`shopify-buy`库，并用您的变量创建一个 Shopify 客户端。

一旦对 Shopify 进行 API 调用，返回的响应将需要进行字符串化和解析，以便以后用作 JSON 对象。因此，创建一个`parseShopifyResponse`函数，这样您就可以在整个应用程序中重复使用它:

```
  import Client from "shopify-buy";
    export const shopifyClient = Client.buildClient({
      storefrontAccessToken: process.env.SHOPIFY_STORE_FRONT_ACCESS_TOKEN,
      domain: process.env.SHOPIFY_STORE_DOMAIN,
    });

    export const parseShopifyResponse = (response) =>  JSON.parse(JSON.stringify(response));

```

让我们继续为您的主页获取所有产品。在 `index.js`中，当页面被请求时，使用 Next.js' `[getServerSideProps](https://nextjs.org/docs/basic-features/data-fetching/get-server-side-props)`进行 API 调用。有了`shopifyClient.product.fetchAll()`，你可以拿到你店里所有的产品。一旦数据返回，它将作为道具传递到您的主页和您的`ProductsList`组件中:

```
  import * as React from 'react';
    import Container from '@mui/material/Container';
    import Box from '@mui/material/Box';

    import Navigation from '../components/Navigation';
    import ProductsList from '../components/ProductsList';
    import { shopifyClient, parseShopifyResponse } from '../lib/shopify'

    export default function Index({products}) {
      return (
        <Box>
          <Navigation />
          <Container maxWidth="lg">
            <ProductsList products={products} />
          </Container>
        </Box>
      );
    }

    export const getServerSideProps = async () => {
      // Fetch all the products
      const products = await shopifyClient.product.fetchAll();

      return {
       props: {
        products: parseShopifyResponse(products),
      },
     };
    };

```

您的虚拟数据的格式与 Shopify 返回的格式不同。你需要让`ProductsList.js`适应这些新数据。在这个文件中，让我们创建一个`Product`组件，并从您的产品中获取 ID、标题、图片、价格和句柄。

Shopify API 的响应可能有点混乱。例如，您产品的价格将在`variants`对象中。[这里的](https://shopify.dev/api/storefront/2022-04/objects/Product#fields)是为了更好地了解产品结构的完整文档。

Shopify 的产品还带有一个在你创建产品时生成的`handle`字段。标有“Black converses”的产品会有一个手柄，如`black-converses`。这对于 SEO 非常有帮助，因为像`productId/1`这样的 URL 的字符串是首选。当导航到您的产品页面时，您将在路线中使用它。

以下是更新后的结果:

```
  import * as React from 'react';
    import { useRouter } from 'next/router'

    import Box from '@mui/material/Box';
    import Typography from '@mui/material/Typography';
    import ImageList from '@mui/material/ImageList';
    import ImageListItem from '@mui/material/ImageListItem';
    import ImageListItemBar from '@mui/material/ImageListItemBar';

    const Product = ({product, goToProductPage}) => {
      const { id, title, images, variants, handle } = product
      const { src: productImage } = images[0]
      const { price } = variants[0]
      return (
        <ImageListItem
          style={{cursor: 'pointer'}}
          onClick={() => goToProductPage(handle)}>
          <img
            src={`${productImage}?w=250&auto=format`}
            srcSet={`${productImage}?w=250&auto=format&dpr=2 2x`}
            alt={title}
            loading="lazy"
          />
          <ImageListItemBar
            title={title}
            subtitle={<span>Price: {price}</span>}
            position="below"
          />
        </ImageListItem>
      )
   }
    export default function ProductsList({products}) {
      const router = useRouter()
      // Navigate to product page with handle i.e /products/black-converses
      const goToProductPage = productHandle => router.push(`/products/${productHandle}`)

      return (
        <Box>
          {
            (products && products.length > 0) ?
            <ImageList cols={5} gap={20}>
              {products.map((product) => (
                <Product
                  key={product.handle}
                  product={product}
                  goToProductPage={goToProductPage}
                />
              ))}
            </ImageList>:
            <Typography variant="body1" align="center">There are no products in this collection</Typography>
          }
        </Box>
      )
    };

```

现在，您可以继续更新您的产品页面。在您的`products/[productHandle].js`中，从您的查询中抓取句柄。Shopify JS SDK 附带了一个名为`fetchByHandle`的函数，它从句柄中获取单个产品。使用你的`productHandle`获得你的产品，并将其添加到你的道具中。

现在，您需要像更新产品列表一样更新您的产品页面。您可以用同样的方式获取标题、图像和价格，并在您的页面上使用它们:

```
    import * as React from 'react';
    import Image from 'next/image'

    import Container from '@mui/material/Container';
    import Box from '@mui/material/Box';
    import Grid from '@mui/material/Grid'
    import Typography from '@mui/material/Typography';
    import Button from '@mui/material/Button';

    import Navigation from '../../components/Navigation';
    import BreadcrumbsNavigation from '../../components/BreadcrumbsNavigation';
    import ProductsList from '../../components/ProductsList';
    import { shopifyClient, parseShopifyResponse } from '../../lib/shopify'

    export default function ProductPage({product}) {

      const { id, title, images, variants, handle } = product
      const { src: productImage } = images[0]
      const { price } = variants[0]

      return (
        <Box>
          <Navigation />
          {product &&
            <Container maxWidth="lg">
              <BreadcrumbsNavigation title={title} />
                <Grid container direction="row">
                  <Grid item xs={6}>
                    <Image
                      src={productImage}
                      alt={`Picture of ${title}`}
                      width={500} automatically provided
                      height={500} automatically provided
                    />
                  </Grid>
                  <Grid item xs={6}>
                    <Typography variant="h3" my={2}>{title}</Typography>
                    <Grid mt={4}>
                      <Typography variant="h6" component="span">Price: </Typography>
                      <Typography variant="body1" component="span">{price}</Typography>
                    </Grid>
                    <Grid mt={1}>
                      <Button variant="contained">Add to cart</Button>
                    </Grid>
                  </Grid>
                </Grid>
            </Container>
          }
        </Box>
      );
    }

    export const getServerSideProps = async ({params}) => {
      const { productHandle } = params
      // Fetch one product
      const product = await shopifyClient.product.fetchByHandle(productHandle);

      return {
       props: {
        product: parseShopifyResponse(product),
      },
     };
    };

```

不幸的是，你的收藏页面需要一点过滤。目前，Shopify JS SDK 只提供了通过集合 ID 获取产品的选项，但没有提供其句柄。在`collections/[collectionName].js`中，你可以使用`client.collection.fetchAllWithProducts()`获取所有的收藏及其产品。

一旦你有了它们，你就可以通过检查它的手柄并把它传给你的道具来抓住正确的那个。

```
    import * as React from 'react';

    import Container from '@mui/material/Container';
    import Box from '@mui/material/Box';

    import Navigation from '../../components/Navigation';
    import BreadcrumbsNavigation from '../../components/BreadcrumbsNavigation';
    import ProductsList from '../../components/ProductsList';
    import { shopifyClient, parseShopifyResponse } from '../../lib/shopify'

    export default function CollectionPage({products, collectionName}) {
      return (
        <Box>
          <Navigation />
          <Container maxWidth="lg">
            <BreadcrumbsNavigation title={collectionName} />
            <ProductsList products={products} />
          </Container>
        </Box>
      );
    }

    export const getServerSideProps = async ({params}) => {
      const { collectionName } = params
      // Fetch all the collections
      const collectionsData = await shopifyClient.collection.fetchAllWithProducts();
      const collections = parseShopifyResponse(collectionsData);
      // Get the right one
      const collection = collections.find(collection => collection.handle === collectionName)

      return {
       props: {
        collectionName,
        products: collection.products,
      },
     };
    };

```

最后，如果您使用的是`Image`组件，您将需要更新`next.config.js`来添加 Shopify 的图像域:

```
   module.exports = {
      reactStrictMode: true,
      images: {
        domains: ['external-content.duckduckgo.com', 'cdn.shopify.com'],
      },
    };

```

最终结果如下所示:

![Navigating Shoe Store](img/1d7cb0dc74de4750ededabfe9b2d9df8.png)

完整的代码库[可以在 GitHub repo 中找到。](https://github.com/mariesta/nextjs-shopify-ecommerce)

## 结论

在本文中，您了解了如何设置 Shopify 商店、启用 Storefront API 以及获取访问令牌。然后，您了解了如何创建一个 Next.js 应用程序，并通过创建一个主页、收藏和产品页面来设置您的前端。最后，您安装了 Shopify JS SDK，并学习了如何使用它从您的在线商店获取产品。

从那里，可以实现更多的功能。无论是创建购物车、向购物车添加产品，还是完成结账，Shopify 的 JS SDK 都可以帮助您完成目标。

## [LogRocket](https://lp.logrocket.com/blg/ecommerce-signup) :看看用户为什么不完成你的电子商务流程中的一个步骤的技术和 UX 原因。

[![](img/d60d88871d85e76e0dcca90f4bbaf78c.png)](https://lp.logrocket.com/blg/ecommerce-signup)

LogRocket 就像是一个网络和移动应用程序和网站的 DVR，记录你的电子商务应用程序上发生的一切。LogRocket 没有猜测用户不转化的原因，而是主动揭示了阻止你转化的根本原因，比如 JavaScript 错误或死点击。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

开始主动监控您的电子商务应用程序— [免费试用](https://lp.logrocket.com/blg/ecommerce-signup)。

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。