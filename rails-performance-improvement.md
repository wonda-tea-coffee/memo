## ruby-prof

spec_helperにこれを仕込んで所望のテストを流す。

```ruby
  config.around(:each) do |example|
    result = RubyProf::Profile.profile do
      example.run
    end

    # print a graph profile to text
    printer = RubyProf::GraphPrinter.new(result)
    printer.print(File.open('tmp/ruby-prof', 'w'), {})
  end
```

大変長いレポートが出るけれど、こんなふうにいきなりメソッドの実行時間割合が変わるタイミングが犯人。  
以下の例では `Kernel#sleep` が支配的であることが分かる。

```
------------------------------------------------------------------------------------------------------------------------------------------------------
                      6.542      6.542      0.000      0.000              5/5     Faraday::Retry::Middleware#call
  89.93%  89.93%      6.542      6.542      0.000      0.000                5     Kernel#sleep                   
------------------------------------------------------------------------------------------------------------------------------------------------------
                      0.005      0.000      0.000      0.005              1/2     RSpec::Core::Example#run_after_example
                      0.405      0.000      0.000      0.405              1/2     RSpec::Core::Example#run_before_example
   5.64%   0.00%      0.410      0.000      0.000      0.410                2     RSpec::Core::Hooks::HookCollections#run /myapp/vendor/bundle/gems/rspec-core-3.13.0/lib/rspec/core/hooks.rb:475
                      0.410      0.000      0.000      0.410              2/2     RSpec::Core::Hooks::HookCollections#run_example_hooks_for
                      0.000      0.000      0.000      0.000              2/2     RSpec::Core::Configuration#dry_run?
                      0.000      0.000      0.000      0.000              2/2     <Module::RSpec>#configuration
                      0.000      0.000      0.000      0.000           2/2037     Symbol#==
------------------------------------------------------------------------------------------------------------------------------------------------------
```
