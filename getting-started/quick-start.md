# Quick Start

## Install

### With Haxelib

`haxelib install tink_web`

### With Lix

`lix install haxelib:tink_web`

## Basic Server-side Router

```haxe
import tink.http.containers.*;
import tink.http.Response;
import tink.web.routing.*;

class Server {
	static function main() {
		var container = new NodeContainer(8080);
		var router = new Router<Root>(new Root());
		container.run(function(req) {
			return router.route(Context.ofRequest(req))
				.recover(OutgoingResponse.reportError);
		});
	}
}

class Root {
	public function new() {}
	
	@:get('/')
	@:get('/$name')
	public function hello(name = 'World')
		return 'Hello, $name!';
}
```

1. Copy the code above and save it as `Server.hx`
1. Build it with: `haxe -js server.js -lib hxnodejs -lib tink_web -main Server`
1. Run the server: `node server.js`
1. Now navigate to `http://localhost:8080` and you should see `Hello, World!`  
  and `http://localhost:8080/Tinkerbell` should print `Hello, Tinkerbell!`  
  
## Basic Client-side Proxy

```haxe
import tink.http.clients.*;
import tink.web.proxy.Remote;
import tink.url.Host;

class Client {
	static function main() {
		var remote = new Remote<Root>(new NodeClient(), new RemoteEndpoint(new Host('httpbin.org', 80)));
		remote.json().handle(function(o) switch o {
			case Success(result): trace(result);
			case Failure(e): trace(e);
		});
	}
}
interface Root {
	@:get('/json')
	function json():Result;
}

typedef Result = {
	slideshow:{
		title:String,
		author:String,
		date:String,
		slides:Array<{
			title:String,
			type:String,
			?items:Array<String>,
		}>,
	}
}
```

1. Copy the code above and save it as `Client.hx`
1. Build it with: `haxe -js client.js -lib hxnodejs -lib tink_web -main Client`
1. Run it: `node client.js`