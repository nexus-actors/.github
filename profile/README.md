<div align="center">

# Nexus

### A typed actor system for PHP 8.5+ &nbsp; `v0.1.0`

Type-safe actors, supervision trees, event sourcing, TCP clustering.<br>
Erlang/OTP and Akka patterns — in the PHP you already know.

[nexusactors.com](https://nexusactors.com) &middot; [Documentation](https://docs.nexusactors.com) &middot; [Quickstart](https://nexusactors.com/quickstart) &middot; [Monorepo](https://github.com/nexus-actors/nexus)

</div>

---

```bash
composer create-project nexus-actors/skeleton my-app
cd my-app
bin/console run
```

An interactive wizard picks your runtime and modules; `make:actor` scaffolds
handler, functional, stateful, and persistent actors.

---

```php
// Define messages
readonly class Ping {
    public function __construct(public ActorRef $replyTo) {}
}
readonly class Pong {}

// Define actor behavior
$pongActor = Behavior::receive(
    static fn(ActorContext $ctx, Ping $msg) => $msg->replyTo->tell(new Pong()) ?? Behavior::same(),
);

// Spawn and interact
$system = ActorSystem::create('my-app', new FiberRuntime());
$ref = $system->spawn(Props::fromBehavior($pongActor), 'pong');
$response = $ref->ask(fn(ActorRef $replyTo) => new Ping($replyTo), Duration::seconds(5));
// $response is Pong
```

---

### Key Features

- **Type-safe actors** — Generic `ActorRef<T>`, `Behavior<T>`, `Props<T>` with Psalm level 1 enforcement
- **Supervision trees** — One-for-one restart strategies with retry windows (all-for-one, escalation, and backoff are in progress)
- **Event sourcing & durable state** — Persist events or state snapshots with automatic recovery on restart
- **Pluggable runtimes** — Write once, run on PHP Fibers (dev), Swoole coroutines (production), or the deterministic Step runtime (tests)
- **TCP clustering** *(experimental)* — Swoole TCP mesh with gossip membership, phi-accrual failure detection, and location-transparent `ClusterRef`
- **Symfony Messenger bridge** *(experimental)* — produce to and consume from any broker Messenger supports
- **OpenTelemetry observability** — traces, metrics, and logs across every actor boundary
- **Custom Psalm plugin** — actor-specific static analysis rules catch concurrency bugs at compile time

### Packages

41 packages, split from the [monorepo](https://github.com/nexus-actors/nexus) and published
independently on [Packagist](https://packagist.org/packages/nexus-actors/) with synchronized
versions. See the [stability matrix](https://nexusactors.com/stability) for the per-package
Stable/Experimental status.

| Start here | |
|---|---|
| [skeleton](https://github.com/nexus-actors/skeleton) | `composer create-project` starter with the setup wizard |
| [core](https://github.com/nexus-actors/core) | Actors, behaviors, supervision, mailboxes |
| [runtime-fiber](https://github.com/nexus-actors/runtime-fiber) | PHP Fiber runtime (development) |
| [runtime-swoole](https://github.com/nexus-actors/runtime-swoole) | Swoole coroutine runtime (production) |
| [persistence](https://github.com/nexus-actors/persistence) | Event sourcing & durable state |
| [maker](https://github.com/nexus-actors/maker) | `make:actor` / `make:message` generators |

> **Maturity:** Nexus is pre-1.0 and under active development. APIs may change between
> minor versions, and several subsystems are experimental — the
> [independent audit](https://github.com/nexus-actors/nexus/blob/main/docs/audits/2026-07-16-nexus-independent-audit.md)
> and its remediation backlog document exactly where things stand.

---

<div align="center">

Backed by [Monadial](https://monadial.com) &middot; MIT License

</div>
