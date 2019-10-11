---
title: JavaScript中的原型与继承
date: 2019-03-27 16:59:31
tags: [js]
---
<h1 id="简介"><a href="#简介" class="headerlink" title="简介"></a>简介</h1>
<p>本节主要讨论JavaScript中继承的几种实现，涉及到原型链的基本知识不过多介绍，一图足以概括。<br><img
        src="/images/prototype/prototype01.jpg" alt></p>
<h1 id="JavaScript中的类"><a href="#JavaScript中的类" class="headerlink"
        title="JavaScript中的类"></a>JavaScript中的类</h1>
<p>JavaScript并不是一门面向对象的语言，因此并没有语言层面上类的概念。但可以通过原型链来模拟类。JavaScript中的类一般为一个构造函数。每个类有三个部分，如下：
</p>
<blockquote>
    <p>1.构造函数内部通过this定义的、供实例化对象复制用的公有属性和方法，不通过this定义的变量为私有属性和方法。<br>2.直接通过点语法添加在构造函数上的静态属性和方法，实例无法访问到。<br>3.添加在构造函数原型上的属性和方法，实例可通过原型链访问，且被所有实例共享。
    </p>
</blockquote>
<h1 id="类式继承"><a href="#类式继承" class="headerlink" title="类式继承"></a>类式继承</h1>
<p>类式继承就是声明两个类，并将一个类的实例赋给另一个类的原型。<br>类式继承的缺点有两个：</p>
<blockquote>
    <p>1.若父类中的公有属性为引用类型，则所有子类实例都会共享该属性。一个实例修改该属性会影响所有实例。<br>2.由于子类实现继承是通过实例化父类实现的，所以在创建父类实例时无法向其传递参数。
    </p>
</blockquote>
<h1 id="构造函数继承"><a href="#构造函数继承" class="headerlink" title="构造函数继承"></a>构造函数继承
</h1>
<p>构造函数继承就是在子类构造函数内部调用父类构造函数（不是创建父类的实例！），并通过 call/apply/bind 改变 this
    的指向并传入参数。<br>构造函数继承的缺点有三个：</p>
<blockquote>
    <p>1.由于没有涉及到 prototype
        ，子类实例并不会继承父类构造函数原型中的方法和属性。<br>2.每个子类实例都相当于拥有一份不同的父类实例，子类之间并不共享。<br>3.instanceof
        操作符无法识别子类实例与父类之间的关系。</p>
</blockquote>
<h1 id="组合继承"><a href="#组合继承" class="headerlink" title="组合继承"></a>组合继承</h1>
<p>组合继承结合了类式继承和构造函数继承，即通过原型链实现对原型属性和方法的继承，通过构造函数实现对实例属性的继承。代码实现上，在子类构造函数中调用父类构造函数，同将父类的实例赋给子类的原型。<br>代码实现如下：<br>
    <figure class="highlight js">
        <table>
            <tr>
                <td class="gutter">
                    <pre><span class="line">1</span><br><span class="line">2</span><br><span class="line">3</span><br><span class="line">4</span><br><span class="line">5</span><br><span class="line">6</span><br><span class="line">7</span><br><span class="line">8</span><br><span class="line">9</span><br><span class="line">10</span><br><span class="line">11</span><br><span class="line">12</span><br><span class="line">13</span><br><span class="line">14</span><br><span class="line">15</span><br><span class="line">16</span><br><span class="line">17</span><br><span class="line">18</span><br><span class="line">19</span><br><span class="line">20</span><br><span class="line">21</span><br></pre>
                </td>
                <td class="code">
                    <pre><span class="line"><span class="comment">// 定义父类</span></span><br><span class="line"><span class="function"><span class="keyword">function</span> <span class="title">superClass</span>(<span class="params"></span>) </span>&#123;</span><br><span class="line">    <span class="comment">// ...</span></span><br><span class="line">&#125;</span><br><span class="line"></span><br><span class="line"><span class="comment">// 定义父类原型方法</span></span><br><span class="line">superClass.prototype.newMethod = <span class="function"><span class="keyword">function</span> (<span class="params"></span>) </span>&#123;</span><br><span class="line">    <span class="comment">// ...</span></span><br><span class="line">&#125;</span><br><span class="line"></span><br><span class="line"><span class="comment">// 定义子类</span></span><br><span class="line"><span class="function"><span class="keyword">function</span> <span class="title">subClass</span> (<span class="params"></span>) </span>&#123;</span><br><span class="line">    <span class="comment">// 继承父类实例属性</span></span><br><span class="line">    superClass.call(<span class="keyword">this</span>); <span class="comment">// 第二次调用superClass()</span></span><br><span class="line">    <span class="comment">// ...</span></span><br><span class="line">&#125;</span><br><span class="line"></span><br><span class="line"><span class="comment">// 继承父类原型属性及方法</span></span><br><span class="line">subClass.prototype = <span class="keyword">new</span> superClass(); <span class="comment">// 第一次调用superClass()</span></span><br><span class="line"><span class="comment">// 修正子类原型指向构造函数的指针</span></span><br><span class="line">subClass.prototype.constructor = subClass;</span><br></pre>
                </td>
            </tr>
        </table>
    </figure>
</p>
<p>组合继承模式避免了类式继承和组合继承的缺点，融合了两者的优点，称为了JavaScript中最常用的继承实现方法。<br>但组合继承也有缺点：</p>
<blockquote>
    <p>第一次调用父类构造函数后，子类原型具有所有父类的实例属性。<br>但第二次调用时，会在子类实例上重新创建这些实例属性，并覆盖掉子类原型中的之前创建的父类实例属性。
    </p>
</blockquote>
<h1 id="原型式继承"><a href="#原型式继承" class="headerlink" title="原型式继承"></a>原型式继承</h1>
<p>ES5中通过新增 Object.create()
    规范了原型式继承。<br>原型式继承是对类式继承的封装，由道格拉斯·克洛弗德在2006年提出。中心思想是借助原型可以基于原有对象创建对象，同时还不用因此创建自定义类型。代码实现如下：<br>
    <figure class="highlight js">
        <table>
            <tr>
                <td class="gutter">
                    <pre><span class="line">1</span><br><span class="line">2</span><br><span class="line">3</span><br><span class="line">4</span><br><span class="line">5</span><br><span class="line">6</span><br><span class="line">7</span><br></pre>
                </td>
                <td class="code">
                    <pre><span class="line"><span class="function"><span class="keyword">function</span> <span class="title">object</span>(<span class="params">o</span>) </span>&#123;</span><br><span class="line">    <span class="comment">// 创建子类构造函数</span></span><br><span class="line">    <span class="function"><span class="keyword">function</span> <span class="title">F</span>(<span class="params"></span>) </span>&#123;&#125;</span><br><span class="line">    <span class="comment">// 子类原型继承父类</span></span><br><span class="line">    F.prototype = o;</span><br><span class="line">    <span class="keyword">return</span> <span class="keyword">new</span> F()</span><br><span class="line">&#125;</span><br></pre>
                </td>
            </tr>
        </table>
    </figure>
</p>
<p>在这段代码中，过渡函数 F
    充当了子类的作用。调用该函数可以得到一个把参数对象作为原型的对象实例。<br>但由于其本质上还是类式继承，因此类式继承的两个缺点仍然存在。因此有了寄生式继承。
</p>
<h1 id="寄生式继承"><a href="#寄生式继承" class="headerlink" title="寄生式继承"></a>寄生式继承</h1>
<p>寄生式原型本质是对原型继承的二次封装。不同的是，在第二层封装中对新对象进行了一些扩展。代码实现如下：<br>
    <figure class="highlight js">
        <table>
            <tr>
                <td class="gutter">
                    <pre><span class="line">1</span><br><span class="line">2</span><br><span class="line">3</span><br><span class="line">4</span><br><span class="line">5</span><br><span class="line">6</span><br><span class="line">7</span><br><span class="line">8</span><br><span class="line">9</span><br></pre>
                </td>
                <td class="code">
                    <pre><span class="line"><span class="function"><span class="keyword">function</span> <span class="title">object</span>(<span class="params">o</span>) </span>&#123;</span><br><span class="line">    <span class="comment">// 创建以参数对象为原型的新对象</span></span><br><span class="line">    <span class="keyword">let</span> obj = <span class="built_in">Object</span>.create(o);</span><br><span class="line">    <span class="comment">// 增强新对象</span></span><br><span class="line">    obj.newMethod = <span class="function"><span class="keyword">function</span> (<span class="params"></span>) </span>&#123;</span><br><span class="line">        <span class="comment">//...</span></span><br><span class="line">    &#125;</span><br><span class="line">    <span class="keyword">return</span> obj</span><br><span class="line">&#125;</span><br></pre>
                </td>
            </tr>
        </table>
    </figure>
</p>
<p>寄生式继承会因为无法做到在函数复用而降级效率，这一点与构造函数继承类似。</p>
<h1 id="寄生组合式继承"><a href="#寄生组合式继承" class="headerlink"
        title="寄生组合式继承"></a>寄生组合式继承</h1>
<p>寄生组合式继承的基本思想是，既然要继承父类原型的方法和属性，那么我们需要的就只是父类的原型而已。代码实现如下：<br>
    <figure class="highlight js">
        <table>
            <tr>
                <td class="gutter">
                    <pre><span class="line">1</span><br><span class="line">2</span><br><span class="line">3</span><br><span class="line">4</span><br><span class="line">5</span><br><span class="line">6</span><br><span class="line">7</span><br><span class="line">8</span><br><span class="line">9</span><br><span class="line">10</span><br><span class="line">11</span><br><span class="line">12</span><br><span class="line">13</span><br><span class="line">14</span><br><span class="line">15</span><br><span class="line">16</span><br></pre>
                </td>
                <td class="code">
                    <pre><span class="line"><span class="comment">// 定义父类</span></span><br><span class="line"><span class="function"><span class="keyword">function</span> <span class="title">superClass</span>(<span class="params"></span>) </span>&#123;</span><br><span class="line">    <span class="comment">// ...</span></span><br><span class="line">&#125;</span><br><span class="line"></span><br><span class="line"><span class="comment">// 定义子类</span></span><br><span class="line"><span class="function"><span class="keyword">function</span> <span class="title">subClass</span>(<span class="params"></span>) </span>&#123;</span><br><span class="line">    <span class="comment">// 继承父类实例属性</span></span><br><span class="line">    superClass.call(<span class="keyword">this</span>)</span><br><span class="line">    <span class="comment">// ...</span></span><br><span class="line">&#125;</span><br><span class="line"></span><br><span class="line"><span class="comment">// 继承父类原型</span></span><br><span class="line">subClass.prototype = <span class="built_in">Object</span>.create(superClass.prototype);</span><br><span class="line"><span class="comment">// 修正父类原型构造函数指针的指向</span></span><br><span class="line">subClass.prototype.consturctor = subClass;</span><br></pre>
                </td>
            </tr>
        </table>
    </figure>
</p>
<p>寄生组合式继承只调用了一次父类构造函数，因此避免了在子类原型上重复创建不必要的属性和方法。<br>寄生组合式继承被开发人员认为是实现继承的最佳实现。
</p>
<h1 id="ES6中的class"><a href="#ES6中的class" class="headerlink"
        title="ES6中的class"></a>ES6中的class</h1>
<p>ES6中新增了类的定义。ES6中的类，完全可以看做构造函数的另一种写法。<br>
    <figure class="highlight js">
        <table>
            <tr>
                <td class="gutter">
                    <pre><span class="line">1</span><br><span class="line">2</span><br><span class="line">3</span><br><span class="line">4</span><br><span class="line">5</span><br><span class="line">6</span><br></pre>
                </td>
                <td class="code">
                    <pre><span class="line"><span class="class"><span class="keyword">class</span> <span class="title">Point</span> </span>&#123;</span><br><span class="line">  <span class="comment">// ...</span></span><br><span class="line">&#125;</span><br><span class="line"></span><br><span class="line"><span class="keyword">typeof</span> Point <span class="comment">// "function"</span></span><br><span class="line">Point === Point.prototype.constructor <span class="comment">// true</span></span><br></pre>
                </td>
            </tr>
        </table>
    </figure>
</p>
<p>class的继承可以通过 <code>extends</code> 关键字实现。采用了寄生组合式模式来实现继承。</p>
</div>
