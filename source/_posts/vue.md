---
title: Vue知识点总结
date: 2019-03-20 17:00:00
tags: [Vue.js]
---
<h1 id="简介"><a href="#简介" class="headerlink" title="简介"></a>简介</h1><p>Vue.js是一个渐进式的、MVVM模式的js框架。与jQuery等框架不同的是，Vue.js将前端开发人员的思想由操作 DOM 为主转变到了操作数据为主。</p>
<h1 id="使用方法"><a href="#使用方法" class="headerlink" title="使用方法"></a>使用方法</h1><ol>
<li><p>方法一</p>
<blockquote>
<p>// 全局安装 vue-cli<br>$ npm install –global vue-cli</p>
</blockquote>
</li>
<li><p>方法二<br>直接引入相应的js文件。</p>
</li>
</ol>
<h1 id="Vue中的基础知识"><a href="#Vue中的基础知识" class="headerlink" title="Vue中的基础知识"></a>Vue中的基础知识</h1><h2 id="基本语法"><a href="#基本语法" class="headerlink" title="基本语法"></a>基本语法</h2><p>Vue采用双花括号对数据进行绑定，又称小胡子（mustache）语法。</p>
<h2 id="常用指令"><a href="#常用指令" class="headerlink" title="常用指令"></a>常用指令</h2><ol>
<li>v-for循环指令</li>
<li>v-if/v-else选择指令</li>
<li>v-on事件绑定指令</li>
<li>v-bind属性绑定指令</li>
<li>v-model双向绑定指令</li>
<li>v-text文本绑定指令</li>
<li>v-html：告知vue这个字符串中含有html标签，不要转义，直接将标签插入到文档中。</li>
<li>v-show：利用display: none/initial进行切换。</li>
<li>v-once：不管是事件触发还是数据绑定，都只发生一次。</li>
<li>v-slot：在组件的模板元素上定义。定义了该指令的组件会被分发到对应的具名内容分发槽中。</li>
</ol>
<h2 id="Vue实例化及创建组件时的常用属性"><a href="#Vue实例化及创建组件时的常用属性" class="headerlink" title="Vue实例化及创建组件时的常用属性"></a>Vue实例化及创建组件时的常用属性</h2><ol>
<li>id：Vue实例绑定的元素的id。</li>
<li>template：定义字符串模板，替换挂载的元素。</li>
<li>data：Vue实例的数据对象，可以是对象或函数（组件必须是函数）。实例创建后，可通过 vm.$data 访问。</li>
<li>props：可以是数组或对象，用于接收来自父组件的数据。</li>
<li>methods：用于定义事件的处理函数。不能使用箭头函数语法定义 methods 函数。实例创建后，可通过 vm.methods-name 访问。</li>
<li>computed：计算属性，算是 data 对象的一部分扩充，定义一个函数或具有 get 和 set 属性的对象，可根据一些变量计算出另一个属性。且计算属性具有缓存功能，只要其依赖的变量不变，多次访问该属性都会立即返回之前计算的结果。</li>
<li>watch：侦听器属性，定义一个函数，其名称为要监听的值。当该值发生变化时，则执行对应的监听函数。其回调会传入属性的新旧值。在 Vue 实例化时会调用 vm.$watch 然后遍历 watch 对象的每一个属性。</li>
<li>filter：局部过滤器。详情见下。</li>
<li>components：局部组件。详情见下。</li>
</ol>
<h2 id="常用后缀"><a href="#常用后缀" class="headerlink" title="常用后缀"></a>常用后缀</h2><ol>
<li>.native：常配合 v-on 使用。用于将一个原生事件绑定到组件的父元素。</li>
</ol>
<h1 id="组件化"><a href="#组件化" class="headerlink" title="组件化"></a>组件化</h1><h2 id="全局组件"><a href="#全局组件" class="headerlink" title="全局组件"></a>全局组件</h2><p>全局组件使用 Vue.component(‘组件名’, {定义}) 注册。全局组件必须在 Vue 实例创建之前声明，才能在 Vue 实例对应的根元素下生效。</p>
<h2 id="局部组件"><a href="#局部组件" class="headerlink" title="局部组件"></a>局部组件</h2><p>局部组件直接在 Vue 实例中用 components 属性注册，其语法为{‘组件名’: {定义}}，且定义中的 data 属性必须是一个函数。</p>
<h2 id="复合组件"><a href="#复合组件" class="headerlink" title="复合组件"></a>复合组件</h2><p>复合组件指在一个组件中调用一个或多个其他组件。把多个组件作为普通HTML元素嵌套调用时需要定义 slot 内容分发槽，这样组件才不会将其填充为其他元素。slot 可当做一般元素使用，可用在父元素定义中，在 slot 首尾标签中间定义的内容为默认呈现的内容。为 <slot> 元素定义 name 属性，则在内容插入时，它会自动寻找组件模板使用 v-slot 定义了相同名称的组件，并将其插入在此位置。</slot></p>
<h2 id="组件通信"><a href="#组件通信" class="headerlink" title="组件通信"></a>组件通信</h2><h3 id="父组件向子组件传值"><a href="#父组件向子组件传值" class="headerlink" title="父组件向子组件传值"></a>父组件向子组件传值</h3><ul>
<li>props<br>props 定义组件需要接受的数据，在父组件调用时通过 v-bind 指令传入，语法为 v-bind:props-name=”props-value” 。注意没有双花括号！！props 为单向数据流，即只能由父组件传数据到子组件，子组件不能通过 props 将数据传入父组件。props 也可进行类型检查，定义一个数据所期望的类型及默认值等信息。</li>
<li>$children<br>在父组件中可通过 $children 访问子组件。<h3 id="子组件向父组件传值"><a href="#子组件向父组件传值" class="headerlink" title="子组件向父组件传值"></a>子组件向父组件传值</h3></li>
<li>自定义事件<br>在父组件调用子组件时，在父组件中定义事件处理函数，使用 @user-event-name 绑定事件处理函数。之后在子组件中使用 $emit(‘user-event-name’, event, arg1) 触发父组件的事件处理函数并传入参数。</li>
<li>修改传入父组件传入的引用类型的值（不要这么做！）<br>父组件向子组件传值时，传入的其实是内存地址。因此，如果传入的是引用类型，在子组件中对其进行修改后也会反映到父组件中。</li>
<li>$parent<br>在子组件中可通过 $parent 访问父组件中的数据。<h2 id="父子组件双向通信"><a href="#父子组件双向通信" class="headerlink" title="父子组件双向通信"></a>父子组件双向通信</h2></li>
<li>v-model 指令<br>在父组件调用子组件时，在子组件上调用 v-model 指令。这样对应的值会传入子组件。且当值在子组件中变化时，父组件中的该值也会变化。但仍需要将该值定义在父组件的 data 以及子组件的 props 中。</li>
<li>.sync修饰符<br>真正的双向绑定可能会带来维护上的问题，因此推荐 update:prop-name 触发事件取而代之。在父组件调用子组件时，使用 v-bind:prop-name.sync 定义。在子组件中使用 this.$emit(‘update:prop-name’, newVal) 触发父组件对对应的数据进行修改。注意：带有 .sync 修饰符的 v-bind 不能和表达式一起使用。</li>
<li>ref<br>在子组件模板中任何元素添加 ref 属性，在实例中使用 this.$refs 属性即可帮助父组件得到子组件中的数据和方法。<h2 id="非父子、兄弟组件之间通信"><a href="#非父子、兄弟组件之间通信" class="headerlink" title="非父子、兄弟组件之间通信"></a>非父子、兄弟组件之间通信</h2></li>
<li>使用一个 Vue 实例作为中央事件总线。<br>新建一个空白 Vue 实例。使用该实例的 $on 监听一个事件。使用该实例的 $emit 触发一个事件。<h2 id="多层父子组件通信"><a href="#多层父子组件通信" class="headerlink" title="多层父子组件通信"></a>多层父子组件通信</h2></li>
<li>$parent + $children<br>使用这两个属性遍历子节点或逐级向上查询父节点，访问到指定组件名时，调用 $emit 触发指定时间。<h2 id="注意"><a href="#注意" class="headerlink" title="注意"></a>注意</h2></li>
<li>如果一个组件 template 中包含多个元素，则应该将多个元素放入同一个父标签中。</li>
<li>组件定义中的 data 属性必须是一个返回对象的函数。</li>
</ul>
<h1 id="生命周期"><a href="#生命周期" class="headerlink" title="生命周期"></a>生命周期</h1><p>Vue 实例的生命周期有六个不同的函数，按触发顺序如下：</p>
<ul>
<li>beforeCreate：在实例初始化之后，数据观测 (data observer) 和 event/watcher 事件配置之前被调用。</li>
<li>created：在实例创建完成后被立即调用。在这一步，实例已完成以下的配置：数据观测 (data observer)，属性和方法的运算，watch/event 事件回调，data 属性加载完成。然而，挂载阶段还没开始，$el 属性目前不可见。</li>
<li>beforeMount：在挂载开始之前被调用，相关的 render 函数首次被调用。</li>
<li>mounted：给 Vue 实例对象添加 $el 成员，并且使用数据替换掉挂载的DOM元素。</li>
<li>beforeUpdate：数据更新时调用，发生在虚拟 DOM 打补丁之前。</li>
<li>updated：由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。</li>
<li>beforeDestroy：在实例销毁之前调用。在这一步，实例仍然完全可用。</li>
<li>destoryed：在Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。</li>
</ul>
<h1 id="过滤器"><a href="#过滤器" class="headerlink" title="过滤器"></a>过滤器</h1><p>过滤器相当于一种特殊的函数，用于双花括号和 v-bind 表达式中的文本格式化。过滤器分为两种，一种是全局过滤器，使用 Vue.filter(‘过滤器名’, 回调函数) 定义；另一种是局部过滤器，使用 filter 属性定义。示例如下：<br><figure class="highlight js"><table><tr><td class="gutter"><pre><span class="line">1</span><br></pre></td><td class="code"><pre><span class="line">&#123;&#123;message | filter(arg1, arg2)&#125;&#125;</span><br></pre></td></tr></table></figure></p>
<p>message 可能不符合此处的标准，要做处理，因此使用过滤器。过滤器的第一个参数为管道符 | 之前的变量，即 message，默认自动传入。之后的参数即为自定义参数。经过处理后，将过滤器的 return 的值插入其中。</p>
<h1 id="自定义指令"><a href="#自定义指令" class="headerlink" title="自定义指令"></a>自定义指令</h1><figure class="highlight js"><table><tr><td class="gutter"><pre><span class="line">1</span><br><span class="line">2</span><br><span class="line">3</span><br><span class="line">4</span><br><span class="line">5</span><br><span class="line">6</span><br><span class="line">7</span><br><span class="line">8</span><br><span class="line">9</span><br><span class="line">10</span><br><span class="line">11</span><br><span class="line">12</span><br><span class="line">13</span><br></pre></td><td class="code"><pre><span class="line">Vue.directive(<span class="string">'change'</span>,&#123;</span><br><span class="line">    bind:<span class="function"><span class="keyword">function</span>(<span class="params">el,bindings</span>)</span>&#123;</span><br><span class="line">    <span class="comment">//首次调用</span></span><br><span class="line">    &#125;,</span><br><span class="line">    update:<span class="function"><span class="keyword">function</span>(<span class="params">el,bindings</span>)</span>&#123;</span><br><span class="line">    <span class="comment">//只要是有数据变化，都会调用</span></span><br><span class="line">    &#125;,</span><br><span class="line">    unbind:<span class="function"><span class="keyword">function</span>(<span class="params"></span>)</span>&#123;</span><br><span class="line">    <span class="comment">//解绑</span></span><br><span class="line">    &#125;</span><br><span class="line">&#125;)</span><br><span class="line"></span><br><span class="line">&lt;any v-change=<span class="string">"count"</span>&gt;&lt;/any&gt;</span><br></pre></td></tr></table></figure>
<h1 id="浏览器事件循环（event-loop）"><a href="#浏览器事件循环（event-loop）" class="headerlink" title="浏览器事件循环（event loop）"></a>浏览器事件循环（event loop）</h1><p>JavaScript是一门单线程语言，但浏览器是事件驱动（Event Driven）的。浏览器中有很多异步的操作，会创建事件并放入执行队列中。浏览器很多异步行为都是通过重开一个线程去完成的。一个浏览器至少常驻三个线程：</p>
<ul>
<li>JavaScript引擎线程</li>
<li>GUI渲染线程</li>
<li>事件触发线程</li>
</ul>
<h2 id="JavaScript引擎"><a href="#JavaScript引擎" class="headerlink" title="JavaScript引擎"></a>JavaScript引擎</h2><p>JavaScript引擎主要有两个部分：内存堆（Memory Heap）和执行栈（Call Stack）。内存堆用于分配内存，执行栈中用于保存当前执行函数的上下文。</p>
<h2 id="事件循环"><a href="#事件循环" class="headerlink" title="事件循环"></a>事件循环</h2><p>事件循环机制是由浏览器实现的。要实现这种机制，需要在程序中设置两个线程：一个负责程序本身的运行，称为“主线程”；另一个负责主线程与其他线程的通信的线程，称为“事件循环（Event Loop）线程”。<br>事件循环的流程可描述为：</p>
<ul>
<li>执行栈中的函数或UI触发事件，JavaScript监听到后，通知Web API将任务添加到事件循环的任务队列中。</li>
<li>事件循环线程监听执行栈是否为空，若为空，则将任务队列中的第一个任务放入执行栈。</li>
</ul>
<p>能够触发添加任务队列的是 setTimeOut、setInterval、setImmediate、I/O、UI事件等。setTimeOut(0) 会将回调函数添加到任务队列的队首，保证执行栈中当前任务执行完后立即执行该回调函数。setTimeOut 和 setInterval 的原理都是一定时间后将回调函数添加到任务队列中，因此即使设置 setTimeOut(0)，回调函数也不会立即执行。</p>
<h1 id="Vue双向绑定实现原理"><a href="#Vue双向绑定实现原理" class="headerlink" title="Vue双向绑定实现原理"></a>Vue双向绑定实现原理</h1><p>Vue通过将订阅-发布模式和数据劫持结合实现双向绑定的。Vue 在初始化时，解析了每个节点的相关指令，并根据初始化模板数据初始化响应的订阅器，且劫持并监听了 data 的所有属性。属性变动后，通知订阅者 watcher，并由订阅者执行相关函数并更新视图。</p>
<h1 id="Virtual-DOM"><a href="#Virtual-DOM" class="headerlink" title="Virtual DOM"></a>Virtual DOM</h1><p>虚拟DOM的本质是对象。Vue 采用虚拟DOM有以下几个原因：<br>（1）把渲染过程抽象画，能够适配除了 DOM 以外的目标。实现跨平台。<br>（2）提升元素的复用程度，做到重复利用模板。<br>（3）集中进行 DOM 操作，优化性能。</p>
<h1 id="组件创建方式总结"><a href="#组件创建方式总结" class="headerlink" title="组件创建方式总结"></a>组件创建方式总结</h1><ol>
<li><p>直接在template属性中指定模板内容<br> 1.全局组件</p>
<figure class="highlight js"><table><tr><td class="gutter"><pre><span class="line">1</span><br></pre></td><td class="code"><pre><span class="line">Vue.component</span><br></pre></td></tr></table></figure>
<p> 2.局部组件</p>
<figure class="highlight js"><table><tr><td class="gutter"><pre><span class="line">1</span><br><span class="line">2</span><br><span class="line">3</span><br><span class="line">4</span><br><span class="line">5</span><br></pre></td><td class="code"><pre><span class="line">&#123;</span><br><span class="line">  components:&#123;</span><br><span class="line">    <span class="string">'my-footer'</span>：&#123;<span class="attr">template</span>:<span class="string">``</span>&#125;</span><br><span class="line">  &#125;</span><br><span class="line">&#125;</span><br></pre></td></tr></table></figure>
</li>
<li><p>.vue结尾的文件</p>
<figure class="highlight js"><table><tr><td class="gutter"><pre><span class="line">1</span><br><span class="line">2</span><br><span class="line">3</span><br></pre></td><td class="code"><pre><span class="line">&lt;template&gt;&lt;/template&gt;</span><br><span class="line">&lt;script&gt;&lt;/script&gt;</span><br><span class="line">&lt;style&gt;&lt;/style&gt;</span><br></pre></td></tr></table></figure>
</li>
<li><p>单独指定一个模板内容</p>
<figure class="highlight js"><table><tr><td class="gutter"><pre><span class="line">1</span><br><span class="line">2</span><br><span class="line">3</span><br><span class="line">4</span><br><span class="line">5</span><br><span class="line">6</span><br><span class="line">7</span><br><span class="line">8</span><br></pre></td><td class="code"><pre><span class="line">&lt;script</span><br><span class="line">id=<span class="string">'myContent'</span></span><br><span class="line">type=<span class="string">'text/x-template'</span>&gt;</span><br><span class="line">&lt;<span class="regexp">/script&gt;</span></span><br><span class="line"><span class="regexp"></span></span><br><span class="line"><span class="regexp">Vue.component('',&#123;</span></span><br><span class="line"><span class="regexp">  template:'#myContent'</span></span><br><span class="line"><span class="regexp">&#125;)</span></span><br></pre></td></tr></table></figure>
</li>
</ol>
