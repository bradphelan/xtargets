--- 
title: Using Webmock to bridge http calls to Rack::Test
date: 09/05/2011
layout: post
--- 

[Webmock](https://github.com/bblimke/webmock) is a neat library for
stubbing out http requests in your application so as not to have
to make real ones during your testing. Normally in one of your models
or controllers you would be making a request to some external webservice
say google.

    stub_request(:get, "http://google.com").to_return("some string")

and then anywhere you make an http request to http://google.com in your
application you get returned "some string". Neat. However we can abuse
this to do something more interesting with Rack::Test. Rack::Test provides

    post
    put
    delete
    head
    get

methods to interact with your system without having to spin up a real
server and the overhead that entail. However if you have a complex
protocol to test for which a ruby client library exists,  
it would be nice to bridge the client library into Rack::Test so 
that you can use it directly instead of having to reinvent
the wheel and call :get, :post, etc directly.

~

Using Webmock this turns out to be very easy and
is shown in the code below.

    module HttpToRack
        # @param method [Symbol] the http method to bridge to Rack::Test.
        # @param uri [String] the end point to intercept
        def http_to_rack(method, uri)

          stub_request(method, uri).to_return( lambda { |request|

            case request.method
            when :post
              post request.uri.path, request.body, request.headers
            when :put
              post request.uri.path, request.body, request.headers
            when :get
              get request.uri.path, request.headers
            when :delete
              delete request.uri.path, request.headers
            when :head
              head request.uri.path, request.headers
            else
              raise "#{request.method} not supported"
            end

            { :body => response.body, 
              :headers => response.headers 
            }

          })
        end
      end
    end

 If you are testing your pingback route for an integration test and there is
some class PingBackDriver that does some fancy XMLRPC for example that the
pingback route responds to then you can use PingBackDriver directly but use
http_to_rack to bridge those specific calls to Rack::Test

     it "should follow the pingback protocol http://www.hixie.ch/specs/pingback/pingback-1.0 " do

       # The post that is the target of the pingback
       http_to_rack :any, "http://www.example.com/posts/1"

       # The pingback server
       http_to_rack :any, "http://www.example.com/pingback"

       PingBackDriver.ping \
                :source_uri => "http://www.example.com/posts/2", 
                :target_uri => "http://www.example.com/posts/1" 

     end
     

