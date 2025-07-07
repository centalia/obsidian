1. Открываем класс ExampleMod
2. Переименовываем пакет (*домен*.*ник*.*название_мода*) 
   После переименовывания Idea, сама перетащит все файлы.
3. Далее вычищаем все лишнее: 
    ~~~ Java
    @Mod(Reindustry.MODID)  
    public class *MOD*{  
      
            public static final String MODID = "*mod-id*"; Индентификатор только в нижнем регистре пишется   
            public static final Logger LOGGER = LogUtils.getLogger();  
      
      
        public Reindustry(){  
      
            IEventBus modEventBus = FMLJavaModLoadingContext.get().getModEventBus();  
            modEventBus.addListener(this::commonSetup);  
            MinecraftForge.EVENT_BUS.register(this);  
      
      
      
            modEventBus.addListener(this::addCreative);  
            ModLoadingContext.get().registerConfig(ModConfig.Type.COMMON, Config.SPEC);  
        }  
      
        private void commonSetup(final FMLCommonSetupEvent event){  
      
        }  
      
        private void addCreative(BuildCreativeModeTabContentsEvent event){  
      
        }  
      
        @SubscribeEvent  
        public void onServerStarting(ServerStartingEvent event){  
      
        }  
      
        @Mod.EventBusSubscriber(modid = MODID, bus = Mod.EventBusSubscriber.Bus.MOD, value = Dist.CLIENT)  
        public static class ClientModEvents{  
      
            @SubscribeEvent  
            public static void onClientSetup(FMLClientSetupEvent event){  
      
            }  
        }  
    }
    
      
~~~
4. В файле gradle.properties заменяем в:
- mod_id (индентификатор мода)
- mod_name (Название мода)
- mod_group_id (пакет ***_домен*.*ник*.*мод*_**)
