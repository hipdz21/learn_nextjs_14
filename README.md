Next.js

(Version: 14)

1.  Cài đặt và cấu hình
    

1.1. Cài đặt

-   Yêu cầu: Node.js >= 18.17
    
-   Có thể cài nextjs thông qua create-next-app hoặc cài thủ công qua npm / yarn
    

(Tìm hiểu thêm)

1.2. Cấu hình nextjs ([API Reference: next.config.js Options | Next.js](https://nextjs.org/docs/14/app/api-reference/next-config-js))

-   Nextjs được cấu hình trong file next.config.js
    
-   Cấu trúc cơ bản:
    

```
// @ts-check
 /** @type {import('next').NextConfig} */
const nextConfig = {
  /* config options here */
}
 module.exports = nextConfig
```

-   Một số option cơ bản
    
    -   reactStrictMode: bật chế độ Strict cho React
        
    -   ```
        reactStrictMode: true,
        ```
        
    -   swcMinify: sử dụng SWC để minify mã, giúp giảm kích thước bundle
        
    -   ```
        swcMinify: true,
        ```
        
    -   Images:
        
        -   Config custom image loader cho Next.js: (use a cloud provider to optimize images instead of using the Next.js built-in Image)
            
        -   ```
            images: {
              loader: 'custom',
              loaderFile: './my/image/loader.js',
            }
            ```
            
        -   Config domains: image chỉ được tải từ những domain này
            
        -   ```
            images: {
              domains: ['example.com'],
            },
            ```
            
    -   async rewrites: chuyển tiếp các request đến url khác
        
    -   ```
        async rewrites() {
          return [
            {
              source: '/api/:path*',
              destination: 'https://api.example.com/:path*',
            },
          ];
        },
        ```
        
    -   env: định nghĩa các biến môi trường có thể truy cập từ client-side
        
    -   ```
        env: {
          MY_API_KEY: process.env.MY_API_KEY,
        },
        ```
        
    -   webpack: config webpack
        
    -   ```
        webpack: (config, { isServer }) => {
          // config webpack
          return config;
        },
        ```
        
    -   i18n
        
    -   ```
        i18n: {
          locales: ['en', 'fr', 'de'],
          defaultLocale: 'en',
        },
        ```
        
    -   compress: nén gzip cho ứng dụng
        
    -   ```
        compress: true,
        ```
        
    -   publicRuntimeConfig: config cho các biến có thể truy cập từ cả client và server
        
    -   ```
        publicRuntimeConfig: {
          apiUrl: process.env.API_URL,
        },
        ```
        
    -   serverRuntimeConfig: config cho các biến có thể truy cập từ server
        
    -   ```
        serverRuntimeConfig: {
          secret: process.env.SECRET,
        },
        ```
        

2.  Routing
    

-   Routing trong nextjs 14 được define qua file page.js (.jsx, .tsx) trong folder “app” (FYI: nextjs ver cũ hơn (<13) define thông qua các file js trong folder “page”)
    

2.1. Routing động và Routing tĩnh

-   Routing tĩnh:
    
    -   Ví dụ:
        
    -   ```
        app/page.js → (/)
        app/about/page.js → (/about)
        ```
        
-   Routing động: Sử dụng [param] trong Tên File, Folder
    
    -   ```
        app/posts/[id]/page.js → Trang cho các bài viết (/posts/1, /posts/2, ...)
        app/posts/[slug].js -> (/posts/a, /posts/b, ...)
        ```
        
    -   Lấy các param thông qua useRouter
        
    -   ```
        // app/posts/[id]/page.js
        import { useRouter } from 'next/router';
        
        const Post = () => {
          const router = useRouter();
          const { id } = router.query; // Lấy tham số từ URL
          return <div>This port id: {id}</div>;
        };
        
        export default Post;
        ```
        
    -   Catch-all Segments: có thể thêm các subsequent segments khi sử dụng … ([...segmentName])
        
    -   ```
        app/shop/[...slug].js -> (/shop/a, /shop/a/b, /shop/a/b/c, ...)
        data của slug sẽ là 1 mảng: vd /shop/a/b/c -> { slug: ['a', 'b', 'c'] }
        ```
        
    -   Optional Catch-all Segments: là 1 optional khi thêm [] ([[...segmentName]])
        
    -   ```
        app/shop/[[...slug]].js	/shop	{ slug: [] }
        app/shop/[[...slug]].js	/shop/a	{ slug: ['a'] }
        app/shop/[[...slug]].js	/shop/a/b	{ slug: ['a', 'b'] }
        app/shop/[[...slug]].js	/shop/a/b/c	{ slug: ['a', 'b', 'c'] }
        ```
        

2.2. Sử dụng nested routes

-   Nested routes là một cách tổ chức các route lồng nhau
    
-   Được define khi tạo file page.js (trừ Catch-all Segments) trong các folder lồng nhau
    
-   Có thể kết hợp Routing động và Routing tĩnh
    

3.  Pages và Component
    

3.1. Page

-   Page là UI duy nhất cho một route. Define bằng cách export default 1 component trong file page.js
    

3.2. Layout

-   Layout: là UI được share với nhiều routes → Khi chuyển hướng: layout sẽ được giữ nguyên trạng thái, không render lại và duy trì tương tác
    
-   Root Layout (Required):
    
    -   Root layout được define ở cấp cao nhất của folder app; nó được áp dụng cho all routes
        
    -   Root layout phải chứa html và body tag
        
-   Nesting Layouts
    
    -   Layout có thể được lồng vào nhau khi add layout.js vào các route segments (folders)
        

3.3. Component (React Component …)

4.  API Routes
    

-   API routes cho phép xây dựng các endpoint API trực tiếp để xử lý dữ liệu và logic phía máy chủ
    
-   Cấu trúc thư mục: thường được đặt trong app/api
    

```
app/
└── api/
    └── hello/
        └── route.js
```

-   Tạo API Route: Tạo file route.js trong các folder trong app/api
    
    -   VD: Tạo file route.js trong thư mục hello
        
    -   ```
        // app/api/hello/route.js
        export async function GET(request) {
          return new Response(JSON.stringify({ message: 'Hello, World!' }), {
            headers: { 'Content-Type': 'application/json' },
          });
        }
        ```
        

5.  Data Fetching
    

6.  Middleware
    
7.  Internationalization
    

9.  Image Optiomization
