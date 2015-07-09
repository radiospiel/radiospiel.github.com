---
layout: post
title: Smoothly installing the "charguess" gem
published: true
date: 2009-11-14
categories:
- charguess
- encoding
- guess
- installing
- ruby
---
<h1>0x2a - Smoothly installing the "charguess" gem</h1>

<p>One of the solutions to guessing a text's encoding in rails is the charguess gem. Sadly, though,
this isn't well prepared to a "gem install charguess" session. The following commands will
gladly download, compile and install the charguess gem on your machine:</p>

<div class="CodeRay">
  <div class="code"><pre>git clone git://github.com/fnando/charguess.git
cd charguess/libcharguess
./configure
make
cd ../gem
ruby extconf.rb --with-charguess-include=../libcharguess --with-charguess-lib=../libcharguess
make
sudo make install</pre></div>
</div>


<p>If you are on Linux you might see an error like this:</p>

<div class="CodeRay">
  <div class="code"><pre>/usr/bin/ld: ../libcharguess/libcharguess.a(charguess.o): relocation R_X86_64_32 against
   `__gxx_personality_v0@@CXXABI_1.3' can not be used when making a shared object; 
   recompile with -fPIC
../libcharguess/libcharguess.a: could not read symbols: Bad value</pre></div>
</div>


<p>in that case you change the <em>./configure</em> line into</p>

<div class="CodeRay">
  <div class="code"><pre>CXXFLAGS=-fPIC ./configure</pre></div>
</div>


<p>And if you like my style: the following gives you String#iconv, with guessing:</p>

<div class="CodeRay">
  <div class="code"><pre>require 'iconv'

begin
  require 'charguess' # not necessary if input encoding is known
rescue MissingSourceFile
  STDERR.puts &quot;install 'charguess', see http://radiospiel.org/0x2a-smooth-charguess-install&quot;
end

class String
  def iconv(output_encoding, input_encoding=nil)
    input_encoding ||= CharGuess::guess(self)                 # =&gt; &quot;windows-1252&quot;
    Iconv.new(output_encoding.to_s, input_encoding.to_s).iconv(self)
  end
end

#
# &quot;some string&quot;.iconv(&quot;utf-8&quot;)</pre></div>
</div>
